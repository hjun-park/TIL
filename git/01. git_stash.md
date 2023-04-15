# git stash

- 로컬 작업내용 임시 저장하는 기능

## 1. `git stash save`

- tracked file (git 저장소에서 변경 사항을 추적하고 있는 상태의 파일)에 있어야 stash 임시 저장 가능
- `No local changes to save` : 새 파일 만든 후 변경 사항을 stash 하려는 경우 발생하는 에러

### 명령어

```shell
$ git stash save [옵션]
# untracked 파일들도 함께 stash
$ git stash --include-untracked
```

### 실행결과

```shell
$ git stash save
Saved working directory and index state WIP on dev: 2fbc529 fix: commit 2번 나가는 문제 수정
```

<br />

## 2. `git stash list`

- 임시 저장된 스냅샷 목록 반환

### 명령어

```shell
$ git stash list
# 실행결과
stash@{0}: WIP on dev: 2fbc529 fix: commit 2번 나가는 문제 수정
```

- `stash@{0}` : stash이름이며 0은 stash의 index를 의미함

<br />

## 3. `git stash show`

- stash 변경사항 확인

### 명령어

```shell
$ git stash show -p stash@{0}
```

### 실행결과

```shell
$ git stash show -p stash@{0}
diff --git a/frontend/pages/user/components/user.vue b/frontend/pages/user/components/user.vue
index ddccca5..c5ff9a7 100644
--- a/frontend/pages/user/components/user.vue
+++ b/frontend/pages/user/components/user.vue
@@ -93,7 +93,7 @@ export default {
             this.fnSearch()
         },
-        /***************** Transaction Function  *********************/
+        /***************** Transaction Function  **********************/
         fnSearch () {
             const params = {
                 USER_ID : this.userid,
```

- `-p` 옵션을 붙이면 patch로 동작, 변경 사항을 확인할 수 있다.
  <br />

## 4. `git stash apply` | `git stash pop`

- `git stash apply` : 변경사항 적용 후 stash list에 남기기
- `git stash pop` : 변경사항 적용 후 stash list에서 삭제
- pop, apply 적용 시에는 merge conflict 발생 우려 존재

### 명령어

```shell
$ git stash pop
```

### 실행결과

```shell
Already up to date.
On branch dev
Your branch is up to date with 'origin/dev'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   frontend/pages/user/components/user.vue
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        frontend/pages/layout/
no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (65a74afbaddc4d6c9805f328c2be0f151099a563)
```

<br />

## 5. `git stash drop [Stash명]` | `git stash clear`
- `git stash drop [Stash명]` : 해당 stash 히스토리 제거
- `git stash clear`: 모든 stash 제거
### 명령어
```shell
git stash clear
```
