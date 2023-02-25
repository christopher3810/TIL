
## Merge와 Rebase의 차이점

<br>

전제\
Main 브랜치에서 feature 브랜치가 분기되고 3번의 commit이 이루어져 있고\
Main 브랜치 역시 feature 브랜치가 분기되고 난뒤 2번의 commit이 이루어졌다.


### Merge


git command

```git
git checkout feature
git merge maian
//or
git merge feature main
```

![merge](https://user-images.githubusercontent.com/61622657/221027556-d46d5a69-4d5d-4941-abd2-34797842b2eb.svg)
출처 : https://www.atlassian.com/git/tutorials/merging-vs-rebasing
<br>
feature 브랜치로 병합시에 main 브랜치와 Feature 브랜치를 병합하는 커밋로그가 Feature의 Head로 추가됩니다.\
변경사항에 대해서 conflict가 날시 당연히 commit한 당사자와 코드 체크를 해서 push해야 합니다.







### Rebase

```git
git checkout feature
git rebase maian
```

<br>


![Rebase](https://user-images.githubusercontent.com/61622657/221027628-94db672b-3b97-4c16-a5c9-84997881d8dc.svg)
출처 : https://www.atlassian.com/git/tutorials/merging-vs-rebasing

말그대로 base를 다시 지정하는 개념입니다.\
feature 브랜치를 분기한 시점에서 변경된 2개의 커밋까지의 최신 main branch 형상을 base로 이후 변경사항이 있는 feature브랜치를 위로 올려서 추가하는 개념입니다.

특정 브랜치를 base로 커밋한 이력을 다시 재정렬 하는 개념 입니다.

<br>


```git
git checkout main
git rebase feature
```

![main을 다른브랜치에 rebase](https://user-images.githubusercontent.com/61622657/221324423-3adfe4ad-5d2c-4482-9e66-fd15f792fa61.svg)
출처 : https://www.atlassian.com/git/tutorials/merging-vs-rebasing

main으로 checkout  한뒤 rebase 를 feature로 잡는경우에는 main의 이력이 변경하게 됩니다.\
main 브랜치가 master인경우 이경우는 피하는게 좋습니다.

<br>

### 참조
***
https://www.atlassian.com/git/tutorials/merging-vs-rebasing
