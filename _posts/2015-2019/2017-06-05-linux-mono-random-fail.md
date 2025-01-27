---
layout: post
title: 'Linux: произвольное падение приложений на mono'
type: post
categories: []
tags:
- linux
- mono
- repetierhost
permalink: "/2017/06/05/linux-%d0%bf%d1%80%d0%be%d0%b8%d0%b7%d0%b2%d0%be%d0%bb%d1%8c%d0%bd%d0%be%d0%b5-%d0%bf%d0%b0%d0%b4%d0%b5%d0%bd%d0%b8%d0%b5-%d0%bf%d1%80%d0%b8%d0%bb%d0%be%d0%b6%d0%b5%d0%bd%d0%b8%d0%b9-mono/"
excerpt: Столкнулся с неочевидной проблемой - раз за разом закрывался RepetierHost
  и не давал напечатать модель на принтере.
---
![53395]({{ site.baseurl }}/assets/images/2017/06/53395.png)Столкнулся со странной проблемой: без сторонней помощи стал закрываться RepetierHost. Ему было все равно печатал он модель, слайсил или просто был открытым. Сам хост я всегда открывал на отдельном воркспейсе и поначалу грешил на то, что какие-то проблемы могут быть у cinnamon, который сейчас использую как дефолтный стол. Если оставить хост отрытым и не менять рабочие столы, не запускать никаких приложений, то все нормально и приложение не отваливалось. Гугл ничего не смог сказать мне про систематический вылеты. Откат на предыдушие версии не помог - они так же вылетали.

Добавил в repetierHost строки, которые перенаправляли лог запуска в файл и он стал выглядеть вот так.

```shell
#!/usr/bin/env bash  
cd /home/penguin/.soft/RepetierHost  
env LANG=en_US.utf8 mono RepetierHost.exe -home {$HOME}.soft/RepetierHost &> {$HOME}/log&
```

После пары запусков я таки поймал в лог креш приложения. Да, не обращаете внимание на то, что принудительно используется локаль en_US - это решает проблему слайсера Slic3r, который по каким-то причинам отказывается работать с группами объектов названными символами, отличными от латинских.

```
/usr/bin/slic3r --load "slic3r_settings.ini" --print-center 100.00,100.00 -o "composition.gcode" "composition.amf"  
Wide character at /usr/lib64/perl5/vendor_perl/Encode.pm line 212.
```

В amf-файле все группы должны иметь латинские имена, а repetier генерирует имена в текущей локали.

Пойманный трейс выглядит следующим образом.

```
Stacktrace:

at <unknown> <0xffffffff>  
at (wrapper managed-to-native) System.Windows.Forms.X11Keyboard.Xutf8LookupString (intptr,System.Windows.Forms.XEvent&,byte[],int,intptr&,System.Windows.Forms.XLookupStatus&) <0x000a4>  
at System.Windows.Forms.X11Keyboard.LookupString (System.Windows.Forms.XEvent&,int,System.Windows.Forms.XKeySym&,System.Windows.Forms.XLookupStatus&) <0x000bb>  
at System.Windows.Forms.X11Keyboard.EventToVkey (System.Windows.Forms.XEvent) <0x0003f>  
at System.Windows.Forms.X11Keyboard.ToUnicode (int,int,string&) <0x0034f>  
at System.Windows.Forms.X11Keyboard.TranslateMessage (System.Windows.Forms.MSG&) <0x0011f>  
at System.Windows.Forms.XplatUIX11.TranslateMessage (System.Windows.Forms.MSG&) <0x00027>  
at System.Windows.Forms.XplatUI.TranslateMessage (System.Windows.Forms.MSG&) <0x00024>  
at System.Windows.Forms.Application.RunLoop (bool,System.Windows.Forms.ApplicationContext) <0x00d6b>  
at System.Windows.Forms.Application.Run (System.Windows.Forms.ApplicationContext) <0x00047>  
at System.Windows.Forms.Application.Run (System.Windows.Forms.Form) <0x00037>  
at RepetierHost.Program.Main (string[]) <0x0003f>  
at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr) <0x000d1>

Native stacktrace:

mono(+0xc3794) [0x55d10cc73794]  
mono(+0x11a8ce) [0x55d10ccca8ce]  
mono(+0x3c633) [0x55d10cbec633]  
/lib64/libpthread.so.0(+0x115c0) [0x7f4b23cea5c0]  
/lib64/libc.so.6(strlen+0x26) [0x7f4b23788fe6]  
/lib64/libX11.so.6(_XimLocalUtf8LookupString+0xde) [0x7f4b1870b8fe]  
[0x4249f8b5]

Debug info from gdb:

[New LWP 5108]  
[New LWP 5112]  
[New LWP 5116]  
[New LWP 5117]  
[New LWP 5118]  
[New LWP 5119]  
[New LWP 5121]  
[New LWP 5122]  
[New LWP 5123]  
warning: File "/usr/bin/mono-sgen-gdb.py" auto-loading has been declined by your `auto-load safe-path' set to "$debugdir:$datadir/auto-load".  
To enable execution of this file add  
add-auto-load-safe-path /usr/bin/mono-sgen-gdb.py  
line to your configuration file "/home/penguin/.gdbinit".  
To completely disable this security protection add  
set auto-load safe-path /  
line to your configuration file "/home/penguin/.gdbinit".  
For more information about this security protection see the  
"Auto-loading safe path" section in the GDB manual.  E.g., run from the shell:  
info "(gdb)Auto-loading safe path"  
warning: File "/usr/bin/mono-sgen-gdb.py" auto-loading has been declined by your `auto-load safe-path' set to "$debugdir:$datadir/auto-load".  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib64/libthread_db.so.1".  
0x00007f4b23ce9fdb in waitpid () from /lib64/libpthread.so.0  
Id   Target Id         Frame  
* 1    Thread 0x7f4b247fa780 (LWP 5104) "mono" 0x00007f4b23ce9fdb in waitpid () from /lib64/libpthread.so.0  
2    Thread 0x7f4b1c3ff700 (LWP 5108) "mono" 0x00007f4b23ce6460 in pthread_cond_wait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0  
3    Thread 0x7f4b1cb62700 (LWP 5112) "Finalizer" 0x00007f4b23ce8957 in do_futex_wait.constprop () from /lib64/libpthread.so.0  
4    Thread 0x7f4b0c301700 (LWP 5116) "Timer-Scheduler" 0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0  
5    Thread 0x7f4b18044700 (LWP 5117) "Timer-Scheduler" 0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0  
6    Thread 0x7f4b06eef700 (LWP 5118) "Threadpool work" 0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0  
7    Thread 0x7f4b06cee700 (LWP 5119) "Threadpool work" 0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0  
8    Thread 0x7f4b05af4700 (LWP 5121) "Threadpool work" 0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0  
9    Thread 0x7f4b06241700 (LWP 5122) "Threadpool work" 0x00007f4b237f801d in poll () from /lib64/libc.so.6  
10   Thread 0x7f4b04c93700 (LWP 5123) "Threadpool work" 0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () from /lib64/libpthread.so.0

Thread 10 (Thread 0x7f4b04c93700 (LWP 5123)):  
#0  0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () at /lib64/libpthread.so.0  
#1  0x000055d10cd6b9ab in worker_thread ()  
#2  0x000055d10cd661d6 in start_wrapper ()  
#3  0x000055d10ce1af4a in inner_start_thread ()  
#4  0x00007f4b23ce06ca in start_thread () at /lib64/libpthread.so.0  
#5  0x00007f4b23803f7f in clone () at /lib64/libc.so.6

Thread 9 (Thread 0x7f4b06241700 (LWP 5122)):  
#0  0x00007f4b237f801d in poll () at /lib64/libc.so.6  
#1  0x000055d10cd6d2f2 in poll_event_wait ()  
#2  0x000055d10cd6e136 in selector_thread ()  
#3  0x000055d10cd661d6 in start_wrapper ()  
#4  0x000055d10ce1af4a in inner_start_thread ()  
#5  0x00007f4b23ce06ca in start_thread () at /lib64/libpthread.so.0  
#6  0x00007f4b23803f7f in clone () at /lib64/libc.so.6

Thread 8 (Thread 0x7f4b05af4700 (LWP 5121)):  
#0  0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () at /lib64/libpthread.so.0  
#1  0x000055d10cd6b9ab in worker_thread ()  
#2  0x000055d10cd661d6 in start_wrapper ()  
#3  0x000055d10ce1af4a in inner_start_thread ()  
#4  0x00007f4b23ce06ca in start_thread () at /lib64/libpthread.so.0  
#5  0x00007f4b23803f7f in clone () at /lib64/libc.so.6

Thread 7 (Thread 0x7f4b06cee700 (LWP 5119)):  
#0  0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () at /lib64/libpthread.so.0  
#1  0x000055d10cd6b9ab in worker_thread ()  
#2  0x000055d10cd661d6 in start_wrapper ()  
#3  0x000055d10ce1af4a in inner_start_thread ()  
#4  0x00007f4b23ce06ca in start_thread () at /lib64/libpthread.so.0  
#5  0x00007f4b23803f7f in clone () at /lib64/libc.so.6

Thread 6 (Thread 0x7f4b06eef700 (LWP 5118)):  
#0  0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () at /lib64/libpthread.so.0  
#1  0x000055d10cd6b9ab in worker_thread ()  
#2  0x000055d10cd661d6 in start_wrapper ()  
#3  0x000055d10ce1af4a in inner_start_thread ()  
#4  0x00007f4b23ce06ca in start_thread () at /lib64/libpthread.so.0  
#5  0x00007f4b23803f7f in clone () at /lib64/libc.so.6

Thread 5 (Thread 0x7f4b18044700 (LWP 5117)):  
#0  0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () at /lib64/libpthread.so.0  
#1  0x000055d10ce19a7d in mono_thread_info_sleep ()  
#2  0x000055d10cd6a91e in monitor_thread ()  
#3  0x000055d10cd661d6 in start_wrapper ()  
#4  0x000055d10ce1af4a in inner_start_thread ()  
#5  0x00007f4b23ce06ca in start_thread () at /lib64/libpthread.so.0  
#6  0x00007f4b23803f7f in clone () at /lib64/libc.so.6

Thread 4 (Thread 0x7f4b0c301700 (LWP 5116)):  
#0  0x00007f4b23ce6809 in pthread_cond_timedwait@@GLIBC_2.3.2 () at /lib64/libpthread.so.0  
#1  0x000055d10cdefb3f in _wapi_handle_timedwait_signal_handle ()  
#2  0x000055d10ce06304 in wapi_WaitForSingleObjectEx ()  
#3  0x000055d10cd659d8 in mono_wait_uninterrupted.isra.18.constprop ()  
#4  0x000055d10cd65aa3 in ves_icall_System_Threading_WaitHandle_WaitOne_internal ()  
#5  0x00000000420b2604 in  ()  
#6  0x0000000000000002 in  ()  
#7  0x0000000000000001 in  ()  
#8  0x0000000000000064 in  ()  
#9  0x00007f4b1c6a7bb0 in  ()  
#10 0x0000000000000063 in  ()  
#11 0x00007f4b08001960 in  ()  
#12 0x00007f4b1c6a7bb0 in  ()  
#13 0x00007f4b0c300660 in  ()  
#14 0x00007f4b0c3005d0 in  ()  
#15 0x00000000420b23d0 in  ()  
#16 0x0000000000000000 in  ()

Thread 3 (Thread 0x7f4b1cb62700 (LWP 5112)):  
#0  0x00007f4b23ce8957 in do_futex_wait.constprop () at /lib64/libpthread.so.0  
#1  0x00007f4b23ce8a04 in __new_sem_wait_slow.constprop.0 () at /lib64/libpthread.so.0  
#2  0x00007f4b23ce8aaa in sem_wait@@GLIBC_2.2.5 () at /lib64/libpthread.so.0  
#3  0x000055d10cd877bb in finalizer_thread ()  
#4  0x000055d10cd661d6 in start_wrapper ()  
#5  0x000055d10ce1af4a in inner_start_thread ()  
#6  0x00007f4b23ce06ca in start_thread () at /lib64/libpthread.so.0  
#7  0x00007f4b23803f7f in clone () at /lib64/libc.so.6

Thread 2 (Thread 0x7f4b1c3ff700 (LWP 5108)):  
#0  0x00007f4b23ce6460 in pthread_cond_wait@@GLIBC_2.3.2 () at /lib64/libpthread.so.0  
#1  0x000055d10cde963f in thread_func ()  
#2  0x00007f4b23ce06ca in start_thread () at /lib64/libpthread.so.0  
#3  0x00007f4b23803f7f in clone () at /lib64/libc.so.6

Thread 1 (Thread 0x7f4b247fa780 (LWP 5104)):  
#0  0x00007f4b23ce9fdb in waitpid () at /lib64/libpthread.so.0  
#1  0x000055d10cc73870 in mono_handle_native_sigsegv ()  
#2  0x000055d10ccca8ce in mono_arch_handle_altstack_exception ()  
#3  0x000055d10cbec633 in mono_sigsegv_signal_handler ()  
#4  0x00007f4b23cea5c0 in <signal handler called> () at /lib64/libpthread.so.0  
#5  0x00007f4b23788fe6 in strlen () at /lib64/libc.so.6  
#6  0x00007f4b1870b8fe in _XimLocalUtf8LookupString () at /lib64/libX11.so.6  
#7  0x000000004249f8b5 in  ()  
#8  0x0000000000000000 in  ()

=================================================================  
Got a SIGSEGV while executing native code. This usually indicates  
a fatal error in the mono runtime or one of the native libraries  
used by your application.  
=================================================================
```

Ок. Теперь уже проще гуглить. Ответ был найден на форуме arch-linux в [ветке](https://bbs.archlinux.org/viewtopic.php?id=213818), которая посвящена keepass: это оказался баг в библиотеке WinForms, который не сильно важен для разработчиков и никто фиксить его пока не будет ([оригинальный репорт](https://bugzilla.xamarin.com/show_bug.cgi?id=41505)).

Временное решение - добавить опцию --verify-all в качестве аргумента mono.

```
env LANG=en_US.utf8 mono --verify-all RepetierHost.exe -home {$HOME}.soft/RepetierHost
```

С этой опцией вылетов не наблюдается, но случаются фризы приложения.

