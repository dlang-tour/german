# D lokal installieren

Der Referenz-Compiler der D Programmiersprache ist DMD (Digital Mars D).
Weiterhin werden die Compiler [LDC](https://github.com/ldc-developers/ldc)
und [GDC](https://gdcproject.org) angeboten, welche einen D Compiler jeweils
mit [LLVM](http://llvm.org) und mit [GCC](https://gcc.gnu.org/) implementieren.
Für Anfänger wird aber DMD empfohlen.

## Installation

Der [Downloadbereich auf dlang.org](https://dlang.org/download.html) bietet
Download-Links zu den verschiedenen D Compilern an. Diese können hier als ausführbare
Dateien für verschiedene Betriebssysteme heruntergeladen und installiert werden.

Zusätlich gibt es ein [Installations-Script](https://dlang.org/install.html) für
verschiedene POSIX-ähnliche Systeme (Linux, FreeBSD, MacOS) welches verschiedene
Versionen und Compiler gleichzeitig installieren kann, ohne Administrationsreche
zu benötigen.

## Pakete

Für verschiedene Betriebssysteme werden auch **Community-basierte Packages** angeboten:

### Windows

* [chocolatey](https://chocolatey.org/packages/dmd): `choco install dmd`

### Mac OS X

* [Homebrew](https://formulae.brew.sh/formula/dmd): `brew install dmd`

### Linux / FreeBSD

DMD kann am schnellsten durch Ausführen von
`curl -fsS https://dlang.org/install.sh | bash -s dmd`
im Benutzerverzeichnis installiert werden.

* [ArchLinux](https://wiki.archlinux.org/index.php/D_(programming_language))
* [Debian/Ubuntu](http://d-apt.sourceforge.net)
* [Fedora/CentOS](https://dlang.org/download.html#dmd)
* [Gentoo](https://wiki.gentoo.org/wiki/Dlang)
* [openSUSE](https://dlang.org/download.html#dmd)

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
