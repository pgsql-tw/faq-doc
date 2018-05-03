# PostgreSQL 支援哪些平台呢？

一般來說，任何現代的 Unix 相容平台都應該能夠運行PostgreSQL。近年來有收到明確的測試平台，可以在[Build farm](http://buildfarm.postgresql.org/)中看到。 該文件中包含有關支持平台的更多細節：[http://www.postgresql.org/docs/current/static/supported-platforms.html](http://www.postgresql.org/docs/current/static/supported-platforms.html).

PostgreSQL 也在 Microsoft Windows NT 相容操作系統運行，像是 Windows XP、Vista、7、8、2003、2008 等系統。這裡有預先包裝好的安裝包：[http://www.postgresql.org/download/windows](http://www.postgresql.org/download/windows).

有用於 Windows 的 Cygwin 版本，但通常不建議；請使用原生 Windows 版本。如果你確實需要用 Cygwin 作為用戶端的應用程序，則可以使用 PostgreSQL 用戶端庫（libpq）的 Cygwin 版本連接到原生的 Windows PostgreSQL。

