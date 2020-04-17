# 😱 Oh My Git

[![GIT](https://img.shields.io/badge/GIT-2.24.1-lightgrey.svg?style=flat-square&logo=GIT&color=F05032)](https://git-scm.com/)
[![macOS](https://img.shields.io/badge/macOS-10.15.4-lightgrey.svg?style=flat-square&logo=Apple&color=999999)](https://www.apple.com/uk/macos/catalina/)

![Oh My Git](https://media.giphy.com/media/MuTenSRsJ7TQQ/giphy.gif)

# Introduction

Welcome to my super useful collection of all things GIT.

These are commands and scripts that I've collected over years of using GIT when I've found myself in situations I don't quite know how to get out of.

### 1. Delete a remote tag

```
git push --delete origin tagname
```

### 2. Overwrite GIT commiter and author information

In your terminal window enter the following and update the `OLD_EMAIL`, `CORRECT_NAME` and `CORRECT_EMAIL` values.

```
#!/bin/sh

git filter-branch -f --env-filter '

OLD_EMAIL="your-old-email@example.com"
CORRECT_NAME="Your Correct Name"
CORRECT_EMAIL="your-correct-email@example.com"

if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
then
export GIT_COMMITTER_NAME="$CORRECT_NAME"
export GIT_COMMITTER_EMAIL="$CORRECT_EMAIL"
fi

if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
then
export GIT_AUTHOR_NAME="$CORRECT_NAME"
export GIT_AUTHOR_EMAIL="$CORRECT_EMAIL"
fi

' --tag-name-filter cat -- --branches --tags
```

Once complete, check your git history and if you're happy run

```
git push --force --tags origin 'refs/heads/*'
```

### 3. Move uncommitted changes from current branch to another branch

```
git stash
git checkout my-branch
git stash pop
```

## 💡 Contributing

1. Fork this repository.
2. Create your feature branch: `git checkout -b my-new-feature`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin my-new-feature`
5. Submit a pull request!

## 📚 Acknowledgements

Changing author information: https://help.github.com/en/github/using-git/changing-author-info.

Delete a remote tag: https://stackoverflow.com/a/5480292.

Metadata badges: https://shields.io/.

The MIT License: https://opensource.org/licenses/MIT.

## ⚖️ License (MIT)

Copyright © Mohamed Kherroubi.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
