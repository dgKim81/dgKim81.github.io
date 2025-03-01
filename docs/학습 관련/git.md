git 명령어

git config -global core.editor "code --wait"
git config -global user.name "name"
git config -globla user.email "email"

git init                    현재 폴더 깃 초기화
git status                  현재 커밋 상태 확인
git log --oneline -n 5      커밋 로그 확인
git add .                   파일 스태시 추가
git commit -a -m "test"     파일 스태시 추가하고 간단한 커밋 멘트로 커밋
git comiit -amend           최신 커밋 다시 쓰기
git branch                  브랜치 목록
git branch -v               브랜치의 현재 커밋 헤드
git branch feature-dev      특정 블랜치로 이동
git switch feature-dev      특정 브랜치로 이동
git switch -c feature-new   브랜치 생성하면서 이동
git branch -D feature-new   브랜치 강제 삭제! -d가 일반 삭제
git diff test.txt           파일 비교 하기
git diff feature-dev feature-new 브랜치간 비교
git diff HEAD HEAD~1        최신 커밋과 이전 커밋 비교하기
git merge feature-dev       현재 브랜치에 대상 브랜치를 가져와서 병합하기기

git stash -> git stach save 스태시 저장
git stash pop               가장 마지막 스태시 적용하고 삭제
git stash apply             pop과 동일한데.. 스테이시를 날리지 않고 적용만 한다.
git stash list              스태시 목록
git stash apply stash@{2}   특정 스태시 적용(삭제 안함)
git stash drop stash@{2}    스태시 삭제
git stash clear             스테시 클리어

git checkout HEAD~2         헤드에서 2번째 커밋으로 이동
git checkout HEAD           최신 커밋으로 헤드 이동
*. checkout으로 HEAD가 아닌 커밋으로 이동하면 Head가 떨어진다.
   
   브랜치로 이동하거나 해당 커밋 위치에서 브랜치를 새로 생성해야 한다.

git restore .               작업 파일 되돌리기
git restore --staged        스태이지된 파일 되돌리기(workdirectory로 변경해줌.)
git reset                   이전 커밋으로 되돌리기(이후 커밋 파일은 그대로 남음)
git reset --hard            이전 커밋으로 되돌리기(위험)
git revert HEAD~2           커밋을 만들면서 이전으로 돌리기

git clone <url>
git remote -v               리포트 목록 보기기
git remote add <name> <url> origin은 그냥 이름이다..
git remote rename <old> <new>
git remote remove <name>
git push <remote> <branch>              원격 브랜치에 올리기기
git push <local-branch>:<remote-branch> 로컬 브랜치를 다른 원격 브랜치에 올릴때
git push -u                             upstream 이걸하고 나면 git push라고만 입력해도 된다. 브랜치를 연결한다.
git push origin --delete <branch-name>  원격 브랜치 삭제하기..
git branch -M <branchName>              현재 브랜치의 이름을 변경

git branch -r                           원격 브랜치 확인
git checkout origin/master              원격 브랜치 포인터로 이동하기
git switch <remoteBranch>               원격 브랜치 연결

git fetch <remote> <branch>             로컬 repository 까지 내려온다.
                                        원격 브랜치가 추가되면 확인 가능함.
git pull <remote> <branch>              로컬 workdirectory 까지 내려온다.


github에 gist라는 기능이 있다 간단하게 코드조각이나 간단한 문서를 같이
공유하기에 매우 좋은 툴인것 같다.!!!!

싱글 브랜치에서 작업 하기(중앙집중형)의 함정.
- 기본 코드를 건드리지 않고는 작업할 수 없다.
- 빈번한 충돌로 인해 머지 작업이 자주 일어난다.
- 원격에서 코드 공유가 어렵다.

Feature 브랜치 작업하기.
메인 브랜치에서 작업하지 않는다.
브랜치에서 마스터에 병합 될 때는 코드가 검토가 되고 승인이 되어야 병합된다.
- 마스터 브랜치의 이력은 공식 프로젝트 이력이다.
- 마스터 브랜치 풀링 없이 단일 기능과 코드 공유로 많은 사람이 협업할 수 있다.
- 동작하지 않는 브랜치는 마스터에 병합하지 않는다.

Feature 작업 데모.
git switch -c "feature/add-nav-bar"
git push origin "feature/add-nav-bar"

git remote -v

git pull origin main
git switch -c "add-pricing-table"

git branch -r
git checkout origin/navbar
git switch -
git switch navbar
- 로컬에 없으면 리모티에 있는 것을 자동으로 셋팅한다.
git push origin-navbar

Merge in Feature Branches(브랜치 병합하기)
- 누구든 원하면 메인에 병합하기. 문제 발생 가능성 높음.
- 프로젝트 오너가 메인에 병합하기.
- pull request 사용하기.

git fetch
git pull origin main
git status
git merge pricing-table
git push origin main

브랜치를 병합하고 나면 브랜치는 삭제한다. 
git branch -d pricing-table

Pull Request (권장)
풀 리퀘스트를 이용하면 코드 리뷰 단계를 거친다.
이건 github 전용 기능이 아니다.
머지를 승인 혹은 반려해야 한다. 

워크플로우
1. feature branch에서 작업을 한다.
2. github의 feature brach에 push up 한다.
3. github에 push 된 feature branch의 pull request를 연다.
4. pull request가 승인되어 병합되기를 기다린다. pull request를 논의한다.
   
Pull Request 생성
1. github에서 compare 버튼을 먼저 클릭한다.
*. @로 맨션을 걸수가 있다.
2. Pull request를 클릭한다.
3. 제목과 메세지를 쓰고 Pull request를 클릭한다.
4. 관리자가 승인하고, 병합한다.
5. 병합 후 브랜치를 삭제 한다.
*. 브랜치에 대한 권한 설정을 해야 함.

pull reqest 충돌 해결하기.
1. can't automatically merge 하지만, create pull request를 클릭한다.
2. 관리자가 병합을 수행한다.
 - browser에서 병합 수행 가능.
 - 로컬에서 처리하기

git fetch origin
git checkout -b <branch> origin/<branch>
git merge main

git commit -m "resolve conflict"
git switch main
git merge --no-ff new-heading
git push orgin main

*. git merge --no-ff new-heading 병합 할 때 빨리 감기가 가능하다고 파악되도라도 
하지말고 지시한다.

3. 작업이 종료되면 pull request는 자동으로 완료 된다.

병합을 병합을 요청한 브랜치로 가서 메인 브랜치를 병합한다
그리고 메인 브랜치로 이동해서 병합된 내용이 있는 브랜치를 병합한다.
git fetach orgin
git switch my-new-feature
git merge master
fix conflicts!

git switch master
git merge my-new-feature
git push origin main

브랜치 보호 규칙 구성하기.
setting > branches
default branch > main

branch protection rule 지정.

Fork란. 
Fork/Clone 
fork는 git의 기능이 아니다. 저장소를 복사한다.
각자 자기의 저장소를 가지고 있다.

Fork/Clone 워크플로우
orignal에 pull request를 보낼 수도 있다.
프로젝트를 포크하고, 로컬에 받으면 origin이 된다. 
1. upstream(가상이름)을 원본으로 등록해서 원본의 변경사항을 pull을 받을 수 있다.
2. 변경사항을 origin에 push한다.
3. github에서 오리진에 pull request를 생성한다. 
4. upstream에서 변경사항을 받아온다.

git remote add upstream

git clone 
git log
git remote -v 
git remote add upstream  <url>
git pull upstream main
git log

git add .
git commit -m "add stevie favorite"
git log 
git remote -v
git push origin main

github에서 pull request를 생성할 수 있다.

git pull upstream main
git branch -M <branchName>              현재 브랜치의 이름을 변경

리베이스 관련 내용.
rebasing - 
황금율이라는 것이 있다.
머지를 대체할 수 있다.
리베이스틑 커밋을 지우는 작업 이다.
git merge 나 리베이스는 병합 한다.
날짜는 그대로 인데.. 순서가 정렬된다.
머지 커밋을 없앤다.
리베이스 새로운 베이스에 브랜치의 커밋을 생성해서 끝에 붙힌다.

feature 브랜치의 새로운 히스토리를 쓴다.

리베이스 장단점, 하면 안돼는 상황
- 병합 커밋 히스토리가 너무 많다면, 브랜치에서 처리한다.
  *. 마스터에 많은 커밋이 생길때, feat브랜치에 머지 커밋을 두지 않고, 변경사항을 가져오기 위해 쓴다.
- 주의! 다른 개발자가 이미 가지고 간 커밋을 리베이스하지 말아야 한다.

git rebase main

git rebase 인터렉티브 내용.
이력을 다시 쓴다.

git rebase -i HEAD~4    4개 커밋전으로 돌아가고 
*. -i 인터렉티브

하나를 변경하면 그것을 참조하는 모든 커밋이 새로 생성된다.

code 창이 열린다.
pick        = 커밋을 사용한다.
reword      = 커밋을 사용하지만 메세지를 수정한다.
edit        = 커밋을 사용하지만 amending 하기위해 중단 된다.
squash      = fixup가 비슷하지만 커밋 메시지를 제거하지 않는다.
fixup       = 이 커밋의 코드를 이전 커밋으로 병합하고, 커밋 메시지만 제거한다.
drop        = 커밋을 제거 한다.

git tag 관련
태크.. 유용한 기능인데..
커밋에 추가하는 라벨이다.
주로 프로젝트 릴리스를 표시하는 용도로 사용된다.
태그는 특정 커밋을 가리키는 포인터이다.

일반태그 : 태그 값만 존재한다.
어노테이드 태그: 주석 태그는 커밋 메시지 처럼 날짜 이메일 메타 데이터를 담아 둔다.

버젼닝에 대해서 얘기해보겠다. 
시맨틱 버젼닝은 번호를 매기는 일종의 규약, 명세 규칙이다.
3개의 숫자와 두개의 점을 의미한다.
시멘틱 버젼의 명세서
3개의 번호가 메이져, 마이너, 패치 로 구성된다.
애플리케이션이나 라이브러리를 최초 배포하면 1.0.0 이다.
패치 번호가 올라가는 것은 사용자가 사용하는데 있어서 의미있는 변경이 없는 것이다.

마이너 릴리즈는 신기능이 추가되었을 때 배포한다. 하위 호환성을 유지해야 한다. 사용자가 바뀌는 것은 없다.

마이너 릴리즈가 올라가면 패치 번호는 0부터 다시 시작한다.

메이져 릴리즈는 하위 호환성이 보장되지 않을 때 올린다.
메이져 릴리즈가 올라가면, 마이너 릴리즈와 패치 릴리즈가 0으로 초기화 된다.

git tag             현저장소의 태그를 모두 검색한다.
git tag -l          -l은 태그를 전부 조회하라는 의미 이다.
git tag -l "*beta*" 태그를 필터해서 가져온다.

git checkout <tag>  태크로 이동할 수 있다.
git diff <tag>..<tag> 태그 비교 할 수 있다.

git tag <tagname>   이건 일반 태그 추가하는 것이다.

git tag -a <tagname> 이건 주석 태크를 붙인다. -m 옵션을 줄 수 있다.
git show <tagname>  커밋 인포를 보는 것이다.!! 그치만 태그 정보도 보인다.

git tag <tagname> <commit hash> 커밋 위치에 태그 달기.
git tag -f <tagname> <commitHash> 강재로 이동 시킨다.
*. 깃태그에서는 같은 이름이 있으면 오류를 발생시킨다.

git tag -d <tagName>  태그를 삭제 시킨다.

git push origin --tags        태그 push 하기
git push origin <tagname> 한개의 태그 push 하기
*. 태그를 git push 명령으로 올라가지 않는다.

git의 내부에 대해서..
로컬 구성 파일 
./git/config 저장소 단위로 설정을 저장할 수 있다.

git config --local user.name "dgkim"
git config --local user.email "dgkim@ttt.aa"

여러가지 설정을 변경할 수 있다.

./git/refs/ 참조.. 브랜치 태그 헤드디렉토리가 있다. 커밋 해쉬값이 있다.
리포트 브렌치 포인터가 존재한다.

HEAD 파일 확인 단순 텍스트 파일인데. 참조값을 가지고 있다.
ref: refs/heads/master

디테치되면 커밋 해시가 나타난다.

objects 테이블 git 핵심 백업, 컨텐트 저장.. 저장소의 모든 데이터를 저장한다.
git은 스냅샷으로 저장한다.
4가지정류의 객체가 들어갈 수 있다.
commit,tree, blob, anotated tag

해시
해시함수.. 다양한 크기의 값을 받아서 고정된 길이의 값을 반환한다.
결정성을 가져야 한다. 항상 결과가 같아야 한다. 입력값이 같다면 결과값이 같다.
단방향 함수 이다.
입력값에 작은 차이가 발생하면 큰차이가 발생해야한다.
다른 두값에 대해서 같은 값이 나오면 안된다.
git은 sha-1을 쓴다. 40자리 16진수 - 언젠가 변경한단다..

git은 key와 value를 저장하는 데이터스토어다.
key로 컨텐트를 요청하는 동작이 핵심 기능이다.
데이터를 저장하는데 그때 키를 반환한다. 필요시 키로 데이터를 조회 한다.

echo 'hello' | git hash-object --stdin
git hash-object <file> 데이터를 저장 한다.

echo 'hello' | git hash-object --stdin -w 
이건 실제로 저장한다.
git objects 폴더 안에 40자리 중 앞자리 두글자는 폴더가 된다.
뒷자리 38자리는 파일명이 된다. 압축되고 암호화 되어 있다.

git cat-file -p <object-hash> 
데이터를 다시 꺼내고 싶을 때...
*. 깃의 해시는 반드시 깃 객체와 연결이 돼 있다.
hash의 일부만 입력해도 데이터를 가져올 수 있다.

그래 파일이 수정되면 hash도 바뀐다. hash가 바뀐거면 파일이 수정된거다.
이걸로 수정여부를 판단할 수 있다.

git cat-file -p <object-hash> > <filename>

blob 블라압 브럽...
파일을 담고 있는 내용 그자체 이다. 파일이름을 저장하지 않는다.
git은 이름과 파일내용을 따로 저장한다.

tree는 디렉토리 내용을 저장한다.
트리는 블럽을 가르키는 포인트와 트리를 가르키는 포인터 둘다 가지고 있다.
트리라고 하는 것은 tree에 node랑 비슷하다. 여튼 디렉토리 또는 파일이름을가지고
해시를 가지고 있다.

git cat-file -p master^{tree} 
트리내용을 볼 수 있다. 마스터의 마지막에 있는 트리를 조회 한다.

git cat-file -t <object-hash> 
타입을 볼수 있다.

commit의 내부
tree      <object-hash>
parent    <commit-hash>
author    <userName>
commtter  <userName>
commit message

지역적이다. 만료도 된다.
git reflog show master
git reset --hard master@{1}

git reflog show flowers flowers@{2}