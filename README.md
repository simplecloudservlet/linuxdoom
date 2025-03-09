
I read this post (https://www.deusinmachina.net/p/lets-compile-linux-doom) and
then I tried to follow it to compile in a recent Ubuntu. After some attempts, it worked and then
I am sharing with you.

Tested in:
-Ubuntu 16.04.y LTS
-Ubuntu 22.04.3 LTS (VirtualBox)

The Setup:

1) Get the source code:

```
$ git clone https://github.com/id-Software/DOOM
```

2) Start compile:

```
$ cd DOOM/linuxdoom-1.10
$ make
```

---
> gcc   -g -Wall -DNORMALUNIX -DLINUX  -c doomdef.c -o linux/doomdef.o
Assembler messages:
Erro fatal: não pode criar linux/doomdef.o: Arquivo ou diretório não encontrado
Makefile:91: recipe for target 'linux/doomdef.o' failed
make: *** [linux/doomdef.o] Error 1
---

3) You need to create the 'linux' directory:

```
$ mkdir linux
```

4) Try 'make' again:

```
$ make
```

---
> i_video.c:49:20: fatal error: errnos.h: Arquivo ou diretório não encontrado
compilation terminated.
Makefile:91: recipe for target 'linux/i_video.o' failed
make: *** [linux/i_video.o] Error 1
---

5) You need to fix this bug in 'i_video.c' in line 49:

Replace:

```
#include <errnos.h>
```

With:

```
#include <errno.h>
```

6) Try 'make' again:

```
$ make
```
---
>m_misc.c:297:45: error: initializer element is not constant
m_misc.c:297:45: note: (near initialization for ‘defaults[40].defaultvalue’)
m_misc.c: In function ‘M_LoadDefaults’:
m_misc.c:395:5: warning: cast from pointer to integer of different size [-Wpointer-to-int-cast]
     (int) newstring;
     ^
Makefile:91: recipe for target 'linux/m_misc.o' failed
make: *** [linux/m_misc.o] Error 1
---

7) A lot of error again. Now, you should go to 'm_misc.c', line 229:

Replace:
```
int defaultvalue;
```

With:
```
long long defaultvalue;
```

8) 'm_misc.c', line 257:

Replace:
```
{"sndserver", (int *) &sndserver_filename, (int) "sndserver"},
```

With:
```
    {"sndserver", (int *) &sndserver_filename, (long long) "sndserver"},
```

9) 'm_misc.c', lines 264 and 265:

Replace:
```
{"mousedev", (int*)&mousedev, (int)"/dev/ttyS0"},
   {"mousetype", (int*)&mousetype, (int)"microsoft"},
```

With:
```
{"mousedev", (int*)&mousedev, (long long)"/dev/ttyS0"},
   {"mousetype", (int*)&mousetype, (long long)"microsoft"},
```

10) 'm_misc.c', lines 288 at 297:

Replace:
```
{"chatmacro0", (int *) &chat_macros[0], (int) HUSTR_CHATMACRO0 },
    {"chatmacro1", (int *) &chat_macros[1], (int) HUSTR_CHATMACRO1 },
    {"chatmacro2", (int *) &chat_macros[2], (int) HUSTR_CHATMACRO2 },
    {"chatmacro3", (int *) &chat_macros[3], (int) HUSTR_CHATMACRO3 },
    {"chatmacro4", (int *) &chat_macros[4], (int) HUSTR_CHATMACRO4 },
    {"chatmacro5", (int *) &chat_macros[5], (int) HUSTR_CHATMACRO5 },
    {"chatmacro6", (int *) &chat_macros[6], (int) HUSTR_CHATMACRO6 },
    {"chatmacro7", (int *) &chat_macros[7], (int) HUSTR_CHATMACRO7 },
    {"chatmacro8", (int *) &chat_macros[8], (int) HUSTR_CHATMACRO8 },
    {"chatmacro9", (int *) &chat_macros[9], (int) HUSTR_CHATMACRO9 }
```

With:
```
{"chatmacro0", (int *) &chat_macros[0], (long long) HUSTR_CHATMACRO0 },
    {"chatmacro1", (int *) &chat_macros[1], (long long) HUSTR_CHATMACRO1 },
    {"chatmacro2", (int *) &chat_macros[2], (long long) HUSTR_CHATMACRO2 },
    {"chatmacro3", (int *) &chat_macros[3], (long long) HUSTR_CHATMACRO3 },
    {"chatmacro4", (int *) &chat_macros[4], (long long) HUSTR_CHATMACRO4 },
    {"chatmacro5", (int *) &chat_macros[5], (long long) HUSTR_CHATMACRO5 },
    {"chatmacro6", (int *) &chat_macros[6], (long long) HUSTR_CHATMACRO6 },
    {"chatmacro7", (int *) &chat_macros[7], (long long) HUSTR_CHATMACRO7 },
    {"chatmacro8", (int *) &chat_macros[8], (long long) HUSTR_CHATMACRO8 },
    {"chatmacro9", (int *) &chat_macros[9], (long long) HUSTR_CHATMACRO9 }
```

11) Try 'make' again:

```
$ make
```

---
> /usr/bin/ld: errno: definição TLS em /lib/x86_64-linux-gnu/libc.so.6 seção .tbss não combina com a referência não-TLS em linux/i_sound.o
/lib/x86_64-linux-gnu/libc.so.6: error adding symbols: Valor inaceitável
collect2: error: ld returned 1 exit status
Makefile:87: recipe for target 'linux/linuxxdoom' failed
make: *** [linux/linuxxdoom] Error 1
---

12) 'i_sound.c' remove the line 166:

```
extern int	errno;
```

Then, append '#include <errno.h>' in the top of file:

```
static const char
rcsid[] = "$Id: i_unix.c,v 1.5 1997/02/03 22:45:10 b1 Exp $";

#include <errno.h>
#include <stdio.h>
```

13) Try 'make' again:

```
$ make
```

14) Ok, it is build. Now it is time to run.

```
$ cd linux
$ ./linuxxdoom
```

---
> DOOM Registered Startup v1.10                           
V_Init: allocate screens.
M_LoadDefaults: Load system defaults.
Z_Init: Init zone memory allocation daemon. 
W_Init: Init WADfiles.
 adding /home/lucio/DOOM2/linuxdoom-1.10/linux/doom.wad
===========================================================================
                 Commercial product - do not distribute!
         Please report software piracy to the SPA: 1-800-388-PIR8
===========================================================================
M_Init: Init miscellaneous info.
R_Init: Init DOOM refresh daemon - [.Falha de segmentação (imagem do núcleo gravada)
---

15) You need to put a 'doom.wad' file inside 'linuxdoom-1.0/linux' folder.
I tried the file 'doom.wad' from this site: https://github.com/Akbar30Bill/DOOM_wads/

16) Inform the .wad path folder:

```
$ export DOOMWADDIR=/path/folder/of/doom.wad
```

17) This step is different from the reference (https://www.deusinmachina.net/p/lets-compile-linux-doom). I observed that 'Segmentation fault' was still obtained, then 
I put a lot of 'printf' in the code to verify where the bug was.

In 'r_data.c' at line 90:

Replace:
```
void		**columndirectory;	// OBSOLETE
```

With:
```
int		columndirectory;	// OBSOLETE
```

18) 'r_data.c' from line 524 to 547. It seems that there is a bug in 'patch' and 'mpatch' when reading textures from .wad files.
So I commented them.

Replace:
```
texture = textures[i] =
	    Z_Malloc (sizeof(texture_t)
		      + sizeof(texpatch_t)*(SHORT(mtexture->patchcount)-1),
		      PU_STATIC, 0);
	
	texture->width = SHORT(mtexture->width);
	texture->height = SHORT(mtexture->height);
	texture->patchcount = SHORT(mtexture->patchcount);

	memcpy (texture->name, mtexture->name, sizeof(texture->name));
	mpatch = &mtexture->patches[0];
	patch = &texture->patches[0];

	for (j=0 ; j<texture->patchcount ; j++, mpatch++, patch++)
	{
	    patch->originx = SHORT(mpatch->originx);
	    patch->originy = SHORT(mpatch->originy);
	    patch->patch = patchlookup[SHORT(mpatch->patch)];
	    if (patch->patch == -1)
	    {
		I_Error ("R_InitTextures: Missing patch in texture %s",
			 texture->name);
	    }
	}
```
With:

```
texture = textures[i] =
  Z_Malloc (sizeof(texture_t),PU_STATIC, 0);
 
//	texture = textures[i] =
//	    Z_Malloc (sizeof(texture_t)
//		      + sizeof(texpatch_t)*(SHORT(mtexture->patchcount)-1),
//		      PU_STATIC, 0);
printf("\n%s","LOG2a");	
	texture->width = SHORT(mtexture->width);
	texture->height = SHORT(mtexture->height);
	//texture->patchcount = SHORT(mtexture->patchcount);

	memcpy (texture->name, mtexture->name, sizeof(texture->name));
	
	//mpatch = &mtexture->patches[0];
	//patch = &texture->patches[0];

	//printf("\n%s %d","LOG3a",mpatch);
	//printf("\n%s %d","LOG3a",patch);
	//printf("\n%s %d","LOG3a",texture->patchcount);

	/*for (j=0 ; j<texture->patchcount ; j++, mpatch++, patch++)
	{
	    patch->originx = SHORT(mpatch->originx);
	    patch->originy = SHORT(mpatch->originy);
	    patch->patch = patchlookup[SHORT(mpatch->patch)];
	    if (patch->patch == -1)
	    {
		I_Error ("R_InitTextures: Missing patch in texture %s",
			 texture->name);
			 }
	    printf("\n%s %d","LOG3",j);
	    }*/
```

19) 'r_data.c': put a '#include <stdint.h>' in the top of this file.

```
static const char
rcsid[] = "$Id: r_data.c,v 1.4 1997/02/03 16:47:55 b1 Exp $";

#include <stdint.h>
#include "i_system.h"
```

20) 'r_data.c' in function 'R_InitColorMaps', replace the cast 'int' with 'intptr_t':

Replace:
```
void R_InitColormaps (void)
{
    int	lump, length;
    
    // Load in the light tables, 
    //  256 byte align tables.
    lump = W_GetNumForName("COLORMAP"); 
    length = W_LumpLength (lump) + 255; 
    colormaps = Z_Malloc (length, PU_STATIC, 0); 
    colormaps = (byte *)( ((int)colormaps + 255)&~0xff); 
    W_ReadLump (lump,colormaps); 
}
```

With:
```
void R_InitColormaps (void)
{
    int	lump, length;
    
    // Load in the light tables, 
    //  256 byte align tables.
    lump = W_GetNumForName("COLORMAP"); 
    length = W_LumpLength (lump) + 255; 
    colormaps = Z_Malloc (length, PU_STATIC, 0); 
    colormaps = (byte *)( ((intptr_t)colormaps + 255)&~0xff); 
    W_ReadLump (lump,colormaps); 
}
```

21) 'r_data.c':

Replace:
```
textures = Z_Malloc (numtextures*4, PU_STATIC, 0);
    texturecolumnlump = Z_Malloc (numtextures*4, PU_STATIC, 0);
    texturecolumnofs = Z_Malloc (numtextures*4, PU_STATIC, 0);
    texturecomposite = Z_Malloc (numtextures*4, PU_STATIC, 0);
```

With:
```
    textures = Z_Malloc (numtextures*sizeof(*textures), PU_STATIC, 0);
    texturecolumnlump = Z_Malloc (numtextures*sizeof(*texturecolumnlump), PU_STATIC, 0);
    texturecolumnofs = Z_Malloc (numtextures*sizeof(*texturecolumnofs), PU_STATIC, 0);
    texturecomposite = Z_Malloc (numtextures*sizeof(*texturecomposite), PU_STATIC, 0);
```

22) In file 'r_draw.c', put a '#include <stdint.h>' in the top of this file.

```
static const char
rcsid[] = "$Id: r_draw.c,v 1.4 1997/02/03 16:47:55 b1 Exp $";

#include <stdint.h>
#include "doomdef.h"
```

23) In file 'r_draw.c' in line 464:

Replace:
```
translationtables = (byte *)(( (int)translationtables + 255 )& ~255);
```

With:
```
translationtables = (byte *)(( (intptr_t)translationtables + 255 )& ~255);
```


24) Try 'make' again.

```
$ make
$ cd linux
$ ./linuxxdoom
```

---
> HU_Init: Setting up heads up display.
ST_Init: Init status bar.
Error: xdoom currently only supports 256-color PseudoColor screens
Falha de segmentação (imagem do núcleo gravada)
---

25) Install the support to simulation of screen:

```
$ apt-get install xserver-xephyr
```

26) Run the simulated screen:

```
$ Xephyr :2 -ac -screen 640x480x8
```

27) In a new terminal, go to the directory of 'linuxxdoom' executable:

```
$ cd linuxdoom-1.0/linux
$ DISPLAY=:2
$ ./linuxxdoom -2
```

28) But the colors are blured. So, in 'i_video.c' in line 819, insert the code:

```
XInstallColormap( X_display, X_cmap );
```

29) In 'p_setup.c' in lin 536:

Replace this:
```
linebuffer = Z_Malloc (total*4, PU_LEVEL, 0);
```

With this new code:
```
linebuffer = Z_Malloc (total*sizeof(*linebuffer), PU_LEVEL, 0);
```

30) Do not forget to save the changes. Then, try the commands again:

```
$ make
$ Xephyr :2 -ac -screen 640x480x8
```

In a new terminal:

```
$ ./linuxxdoom -2
```

31) It is finished this tutorial. I get pixeled walls, probably due 'patch' and 'mpatch'
comments in 'r_data.c', but it was the better that I found to work now. I put the 
the entire code in this repo for further evaluation.

Note: You also could give a try to 'chocolate-doom' that
is simple to install in Ubuntu ($ apt-get install chocolate-doom),
and you can try other .wad files, as heretic and hexen too.
