#git 回滚命令
##还没有push的时候 reset

```
git reset --mix
```
会保留源码,只是将git commit和index 信息回退到了某个版本.
```

git reset --soft
```
保留源码,只回退到commit 信息到某个版本.不涉及index的回退,如果还需要提交,直接commit即可.
```

git reset  --hard
```
源码也会回退到某个版本,commit和index 都回回退到某个版本.(注意,这种方式是改变本地代码仓库源码)


##已经push到以后回滚方式 revert

```
git revert c011eb
```
git revert用一个新提交来消除一个历史提交所做的任何修改.
git revert是用一次新的commit来回滚之前的commit，git reset是直接删除指定的commit

```
git reset a4e215234aa4927c85693dca7b68e9976948a35e MainActivity.java
```
回滚某个文件到指定版本

