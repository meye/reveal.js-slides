## 이제는 Git을 사용해보자!
#### Subversion 저장소를 Git으로 옮기기

2014\. 3\. 5

mEye



## 순서

1. 커미터 목록 정리
2. git-svn으로 Subversion 저장소 가져오기
3. .gitignore 만들기
4. Git bare 저장소를 만들기
5. Subversion 저장소를 새 Git 저장소로 옮기기
6. 태그 정리
7. 마무리



## 커미터 목록 정리 (1/7)

Subversion 저장소에서

```bash
svn log -q | awk -F '|' '/^r/ {sub("^ ", "", $2); \
sub(" $", "", $2); \
print $2" = "$2" <"$2">"}' | sort -u > authors.txt
```


### authors.txt

* Before<!-- .element: class="fragment" data-fragment-index="1" -->
> meye = meye <meye>
* After<!-- .element: class="fragment" data-fragment-index="2" -->
> meye = mEye &lt;meye@dev.meye.net&gt;



## git-svn으로 Subversion 저장소 가져오기 (2/7)

```bash
git svn clone [Subversion 저장소 URL] --no-metadata -A authors.txt --stdlayout ~/temp
```



## .gitignore 만들기 (3/7)

```bash
cd ~/temp
git svn show-ignore -i trunk > .gitignore
git add .gitignore
git commit -m "svn:ignore 설정을 .gitignore 파일로 변환"
```



## Git bare 저장소를 만들기 만들기 (4/7)

```bash
git init --bare ~/new-bare.git
cd ~/new-bare.git
git symbolic-ref HEAD refs/heads/trunk
```



## Subversion 저장소를 새 Git 저장소로 옮기기 (5/7)

```bash
cd ~/temp
git remote add bare ~/new-bare.git
git config remote.bare.push 'refs/remotes/*:refs/heads/*'
git push bare
```


### 정리

```bash
rm ~/temp
```


### master 브랜치

```bash
cd ~/new-bare.git
git branch -m trunk master
```



## 태그 정리 (6/7)

```bash
git for-each-ref --format='%(refname)' refs/heads/tags |
cut -d / -f 4 |
while read ref
do
 git tag "$ref" "refs/heads/tags/$ref";
 git branch -D "tags/$ref";
done
```



## 마무리 (7/7)

끝~ 쉽죠?



## 참고

* [흔한 개발자 이야기 :: Subversion 저장소를 Git으로 옮기기](http://dev.meye.net/entry/Subversion-저장소를-Git으로-옮기기)