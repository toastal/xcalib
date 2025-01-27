# xcalib 0.10
(c) 2004-2007 Stefan D�hla <stefan AT doehla DOT de>
This program is GPL-licensed **postcardware!** More infos at end of README.
This program comes with ABSOLUTELY NO WARRANTY! Use on your own risk.

### purpose
load 'vcgt'-tag of ICC profiles to X-server like MS-Windows or MacOS 
do it to calibrate your display.
Versions 0.5 and higher are also usable with Microsoft Windows. They
can be used as a free alternative to other calibration loaders.

### usage

    xcalib [-options] ICCPROFILE
or

    xcalib [-options] -alter

where the available options are:

* -display <host:dpy>     or -d
* -screen <screen-#>      or -s
* -output <output-#>      or -o
* -clear                  or -c
* -noaction <LUT-size>    or -n
* -verbose                or -v
* -printramps             or -p
* -loss                   or -l
* -invert                 or -i
* -gammacor <gamma>       or -gc
* -brightness <percent>   or -b
* -contrast <percent>     or -co
* -red <gamma> <brightness-percent> <contrast-percent>
* -green <gamma> <brightness-percent> <contrast-percent>
* -blue <gamma> <brightness-percent> <contrast-percent>
* -alter                  or -a
* -help                   or -h
* -version

last parameter MUST be an ICC profile containing a vcgt or mLUT tag
or empty if the "-a" or "-alter" paramter is used or the LUT is to
be cleared.

use profiles gamma\_1\_0.icc and gamma\_2\_2.icc for testing. Profiles
with vcg-tables can be created with most profile creation suites.
An example profile with a vcg-table is inclued, named bluish.icc,
which simulates a very high whitepoint (without further intentions).

!Special note for Win32 users: If there is a default profile set for
the primary display, running xcalib.exe without any arguments will
use the Windows default profile.

If the profile contains gamma values, these values may be related to
a special global gamma value. The default internal correction value
is 1.0, as Apple does (the creator of these calibration tags in
ICC profiles). To resemble the behaviour of the Windows XP Color
Control Panel, pass "-gammacor 2.2" if you think, the screen is way to
bright.

The values that shouldn't alter the screen for gamma, brightness and
contrast are gamma=1.0 brightness=0.0 and contrast=100.0 .
  
### requirements
#### LINUX/UNIXes
  
This program is intended for X11-Servers like XFree86 that come with
XVidModeExtension (e.g. XFree86 4.x.x) and XRandR. Make sure that the
extension is available. Other X-servers like OpenWin do not contain 
XVidMode stuff - so please don't ask me for support if XVidMode isn't
supported. If you are experiencing problems with the X.org server
because of missing XVidMode header files, search for the additional
packages that are available for most distributions. Debian provides
the headers by the package libxxf86vm-dev package.

As of version 0.6, a special treatment for the close-source ATI FGLRX
X11 driver was added. The libs and header files are provided but may
need to be updated for the most current video cards.
The X11 and XVidMode headers are still required.
Thanks to bleader who sent me a patch for multi-monitor support
  
#### Microsoft Windows
 
Since version 0.5, Win32-support was added. The program will work
with most video cards, that have correctly implemented drivers. You
need a working C compiler with the right windows headers to rebuild
xcalib.
Thanks to gl.tter multi-monitor support for Win32 is now available.

### testing
You need a profile which contains the 'vcgt' tag to achieve
monitor calibration. You can create it with some commercial
windows profile creation programs or use the bundled ones from Bruce
Lindbloom (only Gamma 1.0 and 2.2).

If you prefer using free (as the free in open-source) profile
creation programs, have a look at current versions of ArgyllCMS.
It was mentioned that LProf is now also capable of creating monitor
profiles with vcgt tags included.

### install
The bundled Makefile should work on most systems. It is very simple
and doesn't use automake/autoconf stuff. Therefore you and I (the
author) save time writing fancy autoconf/automake tests. One of the
following commands should lead you to a working version of xcalib:

    $ make xcalib
    $ make win\_xcalib
    $ make fglrx\_xcalib
  
For most UNIX-based systems the default version of xcalib should
work. It only uses the XVidMode-Extension. The following command
creates the executable:

    $ make xcalib
  
The Win32 version was made with and tested for MinGW. Since most
users do not have a running MinGW environment, a binary executable is
provided. To compile it on your own, the following command creates a
working Win32-build (tested with MSys):

    $ make win\_xcalib

For ATI's proprietary FGLRX driver for X11, a special version can be
built. Issue the command

    $ make fglrx\_xcalib


### motivation
ICC profiles created and used with MS-Windows can now also be used
with Linux and other unixes with LCMS, Marti's ICC library. Some
applications can already apply profiles (even display profiles) but
they often use standard monitor profiles or custom profiles created
for a calibrated monitor.

Calibration is the motivation for xcalib. Calibration brings your
monitor to a defined state. Color spaces of profiles are often only
valid for this defined state, which was created by a calibration.

Whenever you use a display profile, created with Gretag's
ProfileMaker, AdobeGamma (not that good but better than nothing) or
most other ICC profile creation packages, 'vcgt'-tags are included.
These profiles are only valid with calibration applied!

You get the point?

Once again, a profile contains mainly the characterization of a
device. This characterization should be constant over time for as
long as possible. Therefore, you need a defined state, you can return
to - this is the calibrated state.

Calibration linearizes your device, helps to set characteristics and
can be easily repeated. Repeating calibration can help you to keep
your profile constant (users don't need to change it).
  
The current way of embedding calibration data in a profile is against
the theory of separating calibration from profiling - but we have to 
live with it since it's common. They use a tag called Video Card
Gamma Tag (or vcgt) in ICC-profiles that contains calibration values.
The calibration is applied before profile creation, where the vcgt
  
will be saved in your profile for convenience reasons: All color
settings for the display device are stored in a common file.

So, let's repeat: We have calibration and profile creation - which
are not the same. But calibration helps the user in the profile
creation by having a linear (or linear with respect to a fixed gamma
value) display device.

Linux was missing a tool like 'AdobeGammaLoader' or 'Logo Calibration
Loader' which applied the vcgt-tag to the video-LUT (a matrix which
assigns one color value to another). Under Linux we can not set the
video-LUT itself but a X-server gamma ramp (which does practically
the same).

Applying gamma ramps in the video-LUT has one big advantage: If used
with proper values, all applications benefit from a monitor in a
defined/calibrated state - although not using color management!

But keep the drawback in mind: you will lose some resolution by using
a tool like AdobeGamma over xcalib. This may lead to posterization
artifacts on your display (but doesn't affect printouts). You should
tweak your monitor for perfect linearization as much as possible -
the remaining tweaks might be part of a profile (, the "vcgt" tag). 
  
If you want to come around that drawback: bug your video card vendor
and ask for >8bit LUTs (plus LUTs for every output connector).

### limitations
Not all XFree video card drivers do allow changing the gamma ramps.
And video cards are often having only one LUT for all outputs -
although they are dual-head models.

Known not-working drivers:
- vesa (generic driver without any calibration knobs)
- fglrx (but you can build fglrx\_xcalib)
- savage with other depths than 24 (known bug in savage driver)
  => solution: change DefaultDepth to 24 in xorg.conf if you
  have enough video memory (may not work together with 3D).

If you see both/only one/no display changing it's behaviour, test if
it's a bug of xcalib by cross-checking with the 'xgamma' tool that
comes with every XFree86/X.org distribution.

Custom curves created with AdobeGamma do not contain these in the
vcgt. The vcgt contains 2.2 in all cases, whereas the real LUT-values
that are downloaded to the X-server are located somewhere else in the
system - but nobody know's where. Please tell me where they are - if
you find them. A newer version of AdobeGamma (from 2003) writes a
vcgt tag with useful data. Nevertheless, the size field is wrong. As
of version 0.6, a work-around for wrong AdobeGamma profiles
(created with the 2003 version) was added to the internal parser.

On Win32-systems, some drivers are not correctly implemented. E.g.
the NVidia Riva driver for Windows2000 and WindowsXP wrote nonsense
values to the video cards RAMDAC (resulting in a gray display). I
used for these cards an old NT-driver from a video card vendor.
  
The source code became messy in the last time because of numerous
workarounds and a bad mixture of Win32, X11, ATI code and code for
the different parsers used to get the gamma ramps from the profile.
This makes it hard to find the important code sections for others
than me and might have lead to bugs or leaks. A following version
may be written in C++ to ease modularization of the code and allow
utilization by other software.

### history
#### 0.10: 2018-01-10
- Fix incorrect use of X11 screen and output; rename -s to -o option

#### 0.9: 2014-11-09
- fix rounding errors from upsampling of gamma ramps
- fix -printramps uses integers
- support XRandR

#### 0.8: 2007-08-27
- applied gl.tter's patch for Win32 multi-monitor support
- applied bleader's patch for FGLRX multi-monitor support
- added support for LUTs bigger than in the profile
  linear interpolation is used for creating the values in between
- changed -n parameter: now the size of the simulated LUT must
  be given after -n/-noaction paramter
      
#### 0.7: 2007-06-23
- major code-cleanup
- fixed gamma limits for vcg-formulae
  thanks to Graeme Gill, who pointed out the mistake
- implemented user-changeable gamma correction
- added alteration of existing LUTs
- added parameters for addiditional brightness, contrast, gamma
  either globally or per color
- removed icclib for parsing ICC profiles
- removed the LCMS patch since it doesn't work with newer versions
- check string sizes before strcpy now
- fixed Win32 ICM profile fetching
- added inversion of all values: not related to color-management
  but funny feature for the weekends
- updated this README

#### 0.6: 2005-09-30
- added 0-interpolated upscaling if LUT\>vcgt
- added reading of mLUT instead of vcgt (not memory-safe for now)
  this is used for profiles, created by ProfileMechanic Monitor
- added parsing of obviously wrong profiles made by AdobeGamma
- added fglrx version for ATI's proprietary driver
- added loss calculation (option "-loss" or "-l") which shows how
  many steps are lost by calibrating the device
- added limits of VideoCardGammaFormula to internal parser
    
#### 0.5: 2005-03-03
- Win32 version added (compilable with MinGW)
  + support for command line options as usual
  + support for loading the default display profile
- fixed bug with 8bit vcg-tables in internal and icclib version
  this applied to Pantone ColorVision Spyder profiles only
  thanks to John Ackerman who found this really grave bug
- fixed bug in lcms patch regarding gamma table endpoints
  use a unpatched version of lcms for patching
- introduced macro for little-endian parsing in internal parser
- got rid of some compiler warnings because of multi-byte
  constants
- introduced variables in Makefile so that the user can set
  locations for X11 and patched lcms libs and headers
- beautified code for better readability
- added sample profile which contains a vcg-table
      
#### 0.4: 2005-01-30
- own implementation for parsing the vcgt tags added.
- switch to lcms-1.13 (patched) instead of icclib
  use different make target to activate it
- LUTs and vcgt-content may now have different size.
  LCMS does scaling for xcalib
  this only works with a LCMS build for now
- much icc- and vcgt-stuff was exported to LCMS, to give every user
  the ability to use it and to make xcalib's code less confuscating
  apply the bundled patch to LCMS
- minor README changes to ease readability
    
#### 0.3:
- raise error if no vcg-tag available
- profile no more a necessary parameter after -c and -h option
- some code cleanup

#### 0.2: public pre-release
- added option to reset gamma table to system default (gamma 1.0)
- screen and display can be selected now
- as system gamma for Linux, 1/0.45 = 2.222 is assumed
- bugfixes

#### 0.1:
- initial version with vcg-table and vcg-formula working

### todo
* [ ] integrate the _ICC_PROFILE atom
* [X] interpolated upscaling
* [X] use ATI's API when using the closed-source X11 ATI driver
* [X] further code review for memory leaks
* [x] use platform independent types for better portability
* [X] use lcms instead of icclib: worse vcgt-parsing but functions
      for gamma ramp building and smoothing
* [X] add multi-monitor support to Win32 version
* [X] apply bleader's patch for multiple FGLRX cards

### bugs

if not already mentioned in this README, contact the author if you
find bugs in this software.

### author
Stefan D�hla <stefan AT doehla DOT de>

### thanks

- Graeme Gill for his icclib which I used to parse ICC profiles in
    versions prior 0.4 and which contained vcgt-parsing source code.
    Note that he has a similar tool now, called dispwin. Because
    both tools are similar, he found some ambiguity in the case of
    gamma values and limits in vcgt tags. Thanks, Graeme, keep up
    the good work!

- Kai-Uwe Behrmann for his ideas and alpha-testing
    Note that Kai-Uwe uses xcalib in his Oyranos package

- RRZE (Regionales Rechenzentrum Erlangen) which employed me to ensure
    constant color quality for their large-format-printers and led me
    to color-management using ICC profiles

- My current employer for giving me the bucks I need to buy
    everyday-stuff and bringing happiness to my life

- The X11-team for implementing the gamma ramps (but they weren't
    used!?)

- Gretag/Logosoft for their ProfileMaker software which my former
    employer bought - and MonitorCalibrationTester, to debug vcgt-stuff
    on Win32

- Marti Maria and his lcms-community which led me to the vision of
    having xcalib
    Marti especially for LCMS: his lib for color-management

- Bruce Lindbloom for his ICC-profiles using vcg-formulae

- gl.tter for his work on multi-monitor support for Win32
    see his website at http://gl.tter.org

- bleader for his work on multi-monitor support for ATI's FGLRX

### links
The following links might lead you to other Linux color management
ressources:

* [Little CMS](http://www.littlecms.com)
* [ArgyllCMS](http://www.argyllcms.com)
* [coloraid](http://www.coloraid.de)
* [Kai-Uwe Behrmann](http://www.behrmann.name)
* [LProf](http://lprof.sourceforge.net)

### license

You should have received a copy of the GPL together with this
software. Furthermore, it is postcardware. So if you like this
program, send me a picture postcard from your country/area to:

    Stefan Doehla
    Steinselb 7
    95100 Selb
    GERMANY

Please write on it your name and email-address and that you use
xcalib-0.8 .

EOF
