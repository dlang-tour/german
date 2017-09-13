# D lokal installieren

Auf der [Website der Programmiersprache](https://dlang.org) wird stets die neuste
Version des Referenz-Compilers **DMD** (Digital Mars D) zum [Download](http://dlang.org/download.html)
angeboten und kann wie folgt installiert werden:

### Windows

* [Installer](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd-{{latest-release}}.exe)
* oder: [Archive](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.windows.7z)
* mit [chocolatey](https://chocolatey.org/packages/dmd): `choco install dmd`

### Mac OS X

* `.dmg` [package](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.dmg)
* oder: [Archive](http://downloads.dlang.org/releases/2.x/{{latest-release}}/dmd.{{latest-release}}.osx.tar.xz)
* mit [Homebrew](http://brew.sh): `brew install dmd`

### Linux / FreeBSD

DMD kann am schnellsten durch Ausführen von `curl -fsS https://dlang.org/install.sh | bash -s dmd`
im Benutzerverzeichnis installiert werden.

Für viele Distributionen werden auch Pakete angeboten:

* [ArchLinux](https://wiki.archlinux.org/index.php/D_(programming_language))
* [Debian/Ubuntu](http://d-apt.sourceforge.net)
* [Fedora/CentOS](http://dlang.org/download.html#dmd)
* [Gentoo](https://wiki.gentoo.org/wiki/Dlang)
* [OpenSuse](http://dlang.org/download.html#dmd)

### Andere Compiler

Neben dem Referenz-Compiler DMD, der sein eigenes Backend verwendet, gibt es noch
zwei weitere Compiler, die über den Downloadbereich auf [dlang.org](https://dlang.org/download.html)
heruntergeladen werden können:

* [**GDC**](http://gdcproject.org/downloads) - basierend auf dem GCC-Backend
* [**LDC**](https://github.com/ldc-developers/ldc#installation) - basierend auf dem LLVM-Backend

GDC und LDC verwenden nicht immer das neuste DMD-Frontend, ermöglichen dafür
aber eine bessere Optimierung und unterstützen weitere Prozessorarchitekturen,
+wie z.B. ARM.

Weitere Informationen bietet das [DWiki](https://wiki.dlang.org/Compilers).
