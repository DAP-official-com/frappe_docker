# Some Useful Aliases

startdev() {
	mysql.server start
}

stopdev() {
	mysql.server stop
}

## git

```shell
alias gs="git status -s"
alias ga="git add"
alias gap="git add -p"
alias gc="git commit"
alias gpl="git pull --rebase upstream HEAD"
alias gdw="git diff --word-diff"
alias gdc="git diff --cached"
alias gdcw="git diff --cached --word-diff"
```

## github

```shell
alias pullhub="hub pull-request -b frappe:develop -h [username]:\$(current_branch)"
```

pullreq() {
	# pull request to frappe organization to a particular branch instead of develop
	hub pull-request -b frappe:$1 -h [username]:$(current_branch)
}

```shell
alias pushme="git push origin HEAD"
alias pushup="git push upstream HEAD"
alias pullup="git pull --rebase upstream HEAD"
alias uc="uncommitted ."
```

## Remove all pyc files in directory

```shell
alias rmpyc='find . -name "*.pyc" -exec rm -rf {} \;'
```

## Starts a SMTP server

alias smtpd="sudo python -m smtpd -n -c DebuggingServer localhost:25"


benchstatus() {
	ls -1 | xargs -n1 -I{app} sh -c 'cd {app} && echo "-----" && echo {app} && echo "-----" && git status -s && cd ..'
}

## Disables command history between tabs

unsetopt share_history

## simple python server

serve() {
	python -m SimpleHTTPServer $1
}

[Some Useful Aliases REF:](https://github.com/frappe/erpnext/wiki/Some-Useful-Aliases)