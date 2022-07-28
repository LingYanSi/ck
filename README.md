# ck
git分支快速切换

当你本地有很多分支时，如果你想快速在分支间切换，只需输入分支名的一部分即可


## 使用
```bash
ck mas # 可切换到master分支
```

## 安装
在`.zshrc`文件内添加如下代码

```bash
function ck() {
    branchName=$1

    # 参数为空
    if [ -z $branchName ]; then
        git branch
        return 0
    fi

    # 切回到最近分支
    if [ "$branchName" = "-" ]; then
        git checkout $branchName
        return 0
    fi

    if [ "$2" = "-a" ]; then
        git fetch
        allBranch=`git branch -r`
        realBranch=`echo $allBranch | grep -i $branchName | sed -n '1p' | xargs`

        # 删除remotes/origin/前缀
        # https://stackoverflow.com/questions/13210880/replace-one-substring-for-another-string-in-shell-script
        realBranch=${realBranch/origin\//}
        # https://unix.stackexchange.com/questions/346679/no-coprocess-error-when-using-read
        # https://unix.stackexchange.com/questions/198372/read-command-in-zsh-throws-error
        printf '%s ' "是否切换到远程分支 $realBranch y/n:"
        read yes

        if [ "$yes" = "y" ] || [ "$yes" = "yes" ]; then
            git checkout $realBranch
            return 0
        fi
    else
        # https://stackoverflow.com/questions/11392189/how-to-convert-a-string-from-uppercase-to-lowercase-in-bash
        # branchName=$(echo $branchName | tr '[:upper:]' '[:lower:]')
        allBranch=`git branch`
        # allBranch=$(echo $allBranch | tr '[:upper:]' '[:lower:]')
        # https://stackoverflow.com/questions/369758/how-to-trim-whitespace-from-a-bash-variable
        # grep -i 忽略大小写
        realBranch=`echo $allBranch | grep -i $branchName | sed -n '1p' | xargs`

        git checkout $realBranch

        if [ -z $realBranch ]; then
            echo "本地不存在 $branchName 相关分支，正在为您查找相关远程分支"
            ck $branchName -a
            return
        fi
    fi
}
```
