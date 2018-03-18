# Wordpress Dev Setup

*Context*

macOS 10.13+

Media Temple Grid: Shared Hosting

### Environment Setup
`brew install php`
`brew install mysql`

### Deployment Setup
`ssh yourdomain.com@yourdomain.com`

On server:
```
yourdomain.com:cd ~/domains/yourdomain.com/html
yourdomain.com:git init
yourdomain.com:cd ~/domains/yourdomain.com/repo
yourdomain.com:git --bare init
yourdomain.com:cd ~/domains/yourdomain.com/html
yourdomain.com:git remote add hub ~/domains/yourdomain.com/repo.git
yourdomain.com:touch ~/domains/yourdomain.com/html/.git/hooks/post-commit
yourdomain.com:vim ~/domains/yourdomain.com/html/.git/hooks/post-commit
```
```
#!/bin/bash
echo
echo "**** pushing changes to Hub"
echo

git push hub
```
```
yourdomain.com:touch ~/domains/yourdomain.com/repo.git/hoods/post-update
yourdomain.com:vim ~/domains/yourdomain.com/repo.git/hooks/post-update
```
```
#!/bin/bash
echo
echo "**** Deploying master to /html"
echo
cd ~/domains/yourdomain.com/html || exit
unset GIT_DIR
git pull hub master
git update-server-info
```

On local machine:

`git clone ssh://yourdomain.com@yourdomain.com/~/domains/yourdomain.com/repo.git yourdomain.com`

Now pushing code to remote deploys to html director



###### References
http://joemaller.com/990/a-web-focused-git-workflow/

https://ajfleming.info/git-deployments-on-mediatemple/

