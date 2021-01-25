# syncgw DosBox edition 1.94

Our edition is based on [DosBox 0.74-3](https://sourceforge.net/projects/dosbox/files/dosbox/) source code. Everything is ready 
to be compiled with [MinGW](https://sourceforge.net/projects/mingw/files/latest/download?source=files) under Win 10. 
If you do not want to compile your version, you may also download the ready-to-use executables from the ``exe`` directory.

If want to know how I created this DosBox version, please check out ``docs/HowTo Compile.pdf``.

All patches are controlled by ``#define`` statements in file ``include/syncgw.h``.

```
#define SYNCGW_VER VERSION " - syncgw.com Edition 1.94"
```
** Description: **
Changes DosBox window title to show edition name.

```
#define SYNCGW_CLIPB
```
**Description:** 
Enable read/write access to Windows Clipboard. This patch has some limitations:

* Works only on Windows host operating systems
* Supports only text read/write to/from clip board

**Testing:**

1. Start ``notepad.exe`` in Windows
2. Open DosBox
3. Enter ``copy z:\autoexec.bat clpboard``
4. Switch to notepad and enter ``<CTRL><V>``
5. You should see content of autoexec.bat
6. Modify content and mark block; enter ``<CTRL><C>``
7. Switch to DosBox and enter ``copy clpboard con``
8. You should see modified data

```
#define SYNCGW_LPTX
```
** Description: **
Printer output for LPT1-4 is captures and written into a file ``devLPTx.prn`` (x-device 1-4) in the DoxBox directory. 
If print is terminated (DOS program closes the device) a system command (defined in dosbox.conf) is executed to deliver 
output to host printing system. All output from the issued command is echoed to the DosBox console. At termination of DosBox, 
the temporary "device" files will be automatically deleted.

**Configuration:**

1. Compile the DosBox SYNCGW Edition
2. Call dosbox.exe
3. Enter ``config -writeconf dosbox.conf``
4. Load ``dosbox.conf`` into editor
5. Search for ``[printer]`` section
6. Print output is discarded by default:

```        
        ...
        LPT1=disable
        ...
```

Change ``disable`` to a command which sends output file to your host printing system. If you're working on a Windows system, 
you may use ``copy %s LPT1 >Nul``. ``%s`` is replaced at runtime with the full path name to the directory where DosBox.Exe is located.
Review parameter ``printer_timeout``. It specifies how long print output will be "spooled" before output is delivered to host printing system (1000 ticks is approx. 1 second).

** Some hints: **

* The ``>Nul`` parameter is used to minimize output to DosBox console (error message will still be shown on DosBox console window). 
Check out as required. For debugging purposes you may discard that parameter.
* You may use more complex commands. If the DOS program generated PostScript output and you've only a PCL printer, then you can 
convert the print output by sending print file to GhostScript (which is able to convert output from PostScript to PCL) and then send it to your printer.
* You can easily redirect output from one printer device to a different host system printer device by modifying the output command 
(e.g. ``LPT2=copy %s LPT1 >Nul``).

** Testing: **

1. Specify ``LPT1=type %s`` in dosbox.conf
2. Open DosBox
3. Enter ``copy z:\autoexec.bat LPT2``
4. A message should appear in console window notifying output is discarded (``Output to LPT2 discarded due to configuration settings``)
5. Enter ``copy z:\autoexec.bat LPT1``
6. Content of file should be shown in console window

```
#define SYNCGW_COUNTRY_DE
```

** Description: **
In the good old days of DOS, characters were encoded in one byte (8 bits). At the beginning, a 7-bit character set was defined. This was enough for all English users :roll:. As DOS was spread over the world, requirements for internationalization raised. As you can imagine, the remaining 128 characters available in a byte were far not enough to provide all characters needed in all countries. To provide localization the extended ASCII char set (consisting of 256 different characters) was born, but never standardized. The "new" 128 characters were filled up depending on the code pages used (For a detailed discussion about code page see http://www.freedos.org/freedos/news/technote/141.txt).
     
Those code page has basically nothing to do with DOS file names. File names (and directory names as well) are restricted. Legal characters for DOS file names include the following:
     
* Upper case letters A-Z
* Numbers 0-9
* Space (though trailing spaces are considered to be padding and not a part of the file name)
* ! # $ % & ( ) - @ ^ _ ` { } ~ '
* Values 128-255
     
[Reference](http://en.wikipedia.org/wiki/File_Allocation_Table)
     
MS-DOS (which is only one out of a couple of DOS provided by MicroSoft) makes more restrictions. It does not allow the 
character values 128-255. ([Reference ](http://support.microsoft.com/default.aspx?scid=kb;EN-US;q120138))
     
DOSBox itself supports the MS-DOS approach. If you've ever used a different DOS then you probably need more characters.
     
Another problem arises when trying to create files in the host environment (in my case Win 10). Due to the fact that the 
extended ASCII character set was not properly adapted to the ANSI code used in Win 10, the German characters has different 
code page positions. You may notice this, if you open a DOS text file and the special characters looks very strange.
     
To circumvent this situation are mapping is done by this patch. If you create a file named ``ÄÖÜ`` it would also appear 
as ``ÄÖÜ`` in your Win 10 operating system.
     
Please keep in mind, this patch takes care only about the translation of "Umlaute" characters to the Win 10 file system.
     
** Testing: **

1. Enter ``mount C: C:\DosBox`` in DosBox window
2. Enter ``copy Z:\autoexec.bat c:\äÄöÖüÜß``
3. Enter ``dir c:\äÄöÖüÜß``

```
#define SYNCGW_FILEDATETIME
```

** Description: **
Add missing function (INT 21H, 0x5701) for setting date and time to files.
     
** Testing: **

1. Create two empty directories ``t1`` and ``t2``
2. Copy some files into directory ``t1``
3. Start "Norton Commander" or some other synchronization software
4. Open directory ``t2`` in righ window and ``t1`` in left window
5. Synchronize directory ``t1`` with directory ``t2``
6. Compare directory ``t1`` with directory ``t2``
7. All files should be equal

Please enjoy!

<a href="https://www.paypal.com/donate?hosted_button_id=RQMP8CWD2Y2XC" target="_blank">
  <img src="https://www.paypalobjects.com/en_US/DK/i/btn/btn_donateCC_LG.gif" alt="Donate with PayPal"/>
</a>


