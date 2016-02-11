 🍾 Jeroboam
============

#### Keep's it sparkling for longer

This tool checks all applications and preference panes in the typical OS X
installation paths whether they contain an outdated [MITM-vulnerable][1]
version of Sparkle.
If that's the case, it checks some further heuristics to try to determine
whether the application's updater is actually vulnerable.
In the end, it uses this information to classify the risk, where the
classifications may take the following values:

* *IMMINENT DANGER*: the heuristics indicate that it can't be assured, that
  using the updater won't compromise the system.
  In the best case, the app publisher provides updated binaries with a patched
  version of Sparkle, which isn't vulnerable or avoids using unencrypted
  HTTP connections for retrieving the AppCast, as strongly encouraged by
  Sparkle's documentation.
  Alternative countermeasures may range from disabling auto-updates and not
  using the updater anymore, over restricting the app's network functionalities
  to putting the app under quarantine.

* *UNKNOWN*: the app was compiled against an older SDK, so that [ATS][2] is not
  active or there are exclusions from ATS, while the feed URL for the AppCast
  is not declared in the Info.plist, so it has to be programmatically provided,
  which can't be determined by statical analysis. Nevertheless the tool
  inspects all string literals in the executable to find all HTTP URLs, which
  might include the feed URL. This strategy has limits and can't catch URLs,
  which weren't fully hardcoded as strings or obfuscated in another way. The
  found URLs are printed for further investigation through the user to allow a
  better risk estimation. If an insecure URL serving an AppCast can be found,
  it is likely that the app is vulnerable, otherwise it can't be excluded.

* *BROKEN UPDATER*: the app doesn't have exclusions from ATS while the feed
  URL is insecure and it was compiled against OS X 10.11 SDK, which causes that
  ATS is enabled and insecure HTTP requests are blocked by default. That means
  the updater won't be able to download any updates.


## Usage

```shell
> curl -sSL https://raw.githubusercontent.com/mrackwitz/jeroboam/master/jeroboam | ruby
```

## Exemplary Output

![Exemplary Output](https://raw.github.com/mrackwitz/jeroboam/assets/output.png)

## License

>Copyright (c) 2016 Marius Rackwitz
>
>Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:
>
>The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
>
>THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE

[1]: https://sparkle-project.org/documentation/security/
[2]: https://developer.apple.com/library/prerelease/mac/technotes/App-Transport-Security-Technote/