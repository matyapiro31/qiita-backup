Androidのmkshをbashで置き換えてみた結果、互換性のためこれらをbashに移しました。
\#の付いているのがbashでは不要な別名です。

```bash:alias
autoload='typeset -fu'
functions='typeset -f'
hash='alias -t' #
history='fc -l'#
integer='typeset -i'
l=ls
la='l -a'
ll='l -l'
lo='l -a -l'
local=typeset
login='exec login' #?
nameref='typeset -n'
nohup='nohup ' #
r='fc -e -'
source='PATH=$PATH:. command .' #
type='whence -v' #
```

