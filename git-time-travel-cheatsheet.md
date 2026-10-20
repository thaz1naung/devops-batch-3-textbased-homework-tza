# Git ဖြင့် Time Travel လုပ်ခြင်း — Cheatsheet

1. [Commit ဟောင်းကို ခဏသွားကြည့်ခြင်း](#၁-commit-ဟောင်းကို-ခဏသွားကြည့်ခြင်း)
2. [Commit ဟောင်းကနေ Branch အသစ် ဖန်တီးခြင်း](#၂-commit-ဟောင်းကနေ-branch-အသစ်-ဖန်တီးခြင်း)
3. [Tracked File အားလုံးကို ပြန်ထားခြင်း](#၃-tracked-file-အားလုံးကို-ပြန်ထားခြင်း)
4. [File တစ်ခုကို နောက်ဆုံး Commit အတိုင်း ပြန်ထားခြင်း](#၄-file-တစ်ခုကို-နောက်ဆုံး-commit-အတိုင်း-ပြန်ထားခြင်း)
5. [File တစ်ခုကို Commit ဟောင်းအတိုင်း ပြန်ထားခြင်း](#၅-file-တစ်ခုကို-commit-ဟောင်းအတိုင်း-ပြန်ထားခြင်း)
6. [File ကို Unstage လုပ်ခြင်း](#၆-file-ကို-unstage-လုပ်ခြင်း)
7. [Repository ကို Commit ဟောင်းဆီ Reset လုပ်ခြင်း](#၇-repository-ကို-commit-ဟောင်းဆီ-reset-လုပ်ခြင်း)
8. [Commit ကို Revert လုပ်ခြင်း](#၈-commit-ကို-revert-လုပ်ခြင်း)
9. [Quick Reference](#quick-reference)

---

## ၁။ Commit ဟောင်းကို ခဏသွားကြည့်ခြင်း

ပုံမှန်မှာ `HEAD` က commit ကို တိုက်ရိုက် မညွှန်ပါဘူး။ Branch နာမည် (ဥပမာ `main`) ကို ညွှန်ပြီး branch က commit ကို ညွှန်ပါတယ်။ ဒါကြောင့် commit အသစ်လုပ်ရင် `main` ပါ ရှေ့ကို လိုက်တိုးသွားပါတယ်။

```
ပုံမှန်:          HEAD → main → C3

Detached HEAD:   HEAD → C1      (branch မပါဘဲ commit ကို တိုက်ရိုက်ညွှန်)
```

### Detached HEAD

```bash
git checkout 604a39a
# (or) Git 2.23+
git switch --detach 604a39a
```

`HEAD` က branch ကို မညွှန်တော့ဘဲ commit `604a39a` ကို တိုက်ရိုက် ညွှန်နေပါတယ်။ ဒီအခြေအနေမှာ commit အသစ်လုပ်ရင် ဘယ် branch မှ မပိုင်တဲ့အတွက် branch ဆီ ပြန်ပြောင်းလိုက်တာနဲ့ ရှာရခက်သွားပါမယ်။

### တစ်ဆင့်ချင်း နောက်ပြန်သွားခြင်း

```bash
git checkout HEAD~1
```

`HEAD~1` ဆိုတာ လက်ရှိ commit ရဲ့ parent (တစ်ဆင့် အရင် commit) ဖြစ်ပါတယ်။ `HEAD~2` က နှစ်ဆင့်အရင်၊ စသဖြင့်ပါ။ ဒါလည်း detached HEAD ပဲ ဖြစ်ပါတယ်။

### Branch ဆီ ပြန်လာခြင်း

```bash
git switch main
# (or) ယခင် branch ဆီ ပြန်သွား
git switch -
```

---

## ၂။ Commit ဟောင်းကနေ Branch အသစ် ဖန်တီးခြင်း

Detached HEAD မှာ ရှိနေတုန်း အလုပ်ဆက်လုပ်ချင်ရင် branch အသစ် ဖန်တီးရပါမယ်။

```bash
git switch -c fix-old-bug 604a39a
```

> Detached HEAD မှာ commit လုပ်ပြီးသားဆိုရင်တောင် `git switch -c fix-old-bug` (commit SHA မထည့်ဘဲ) ရိုက်လိုက်ရင် အဲဒီ commit တွေကို branch အသစ်နဲ့ သိမ်းလို့ရပါတယ်။

---

## ၃။ Tracked File အားလုံးကို ပြန်ထားခြင်း

```bash
git reset --hard origin/main
```

> ⚠️ **သတိ:** ဒီ command က "ပြောင်းလဲမှုတွေ ပြန်ထားတာ" ထက် ပိုလုပ်ပါတယ်။ Local branch ကို remote ရဲ့ `main` နဲ့ အတိအကျ တူအောင် လုပ်ပါတယ်။ **Push မလုပ်ရသေးတဲ့ local commit တွေပါ ပျောက်သွားပါမယ်။**
>
> Local commit တွေ မထိဘဲ uncommitted ပြောင်းလဲမှုကိုပဲ ဖျက်ချင်ရင် `git reset --hard` (HEAD သို့) ကို သုံးပါ။

---

## ၄။ File တစ်ခုကို နောက်ဆုံး Commit အတိုင်း ပြန်ထားခြင်း

```bash
git checkout HEAD filename.txt
# (or)
git restore filename.txt
```

| Command | ဘယ်ကနေ ယူလဲ | ဘာကို ပြောင်းလဲ |
|---|---|---|
| `git checkout HEAD filename.txt` | နောက်ဆုံး commit (`HEAD`) | Working directory ရော staging area ပါ |
| `git restore filename.txt` | Staging area | Working directory ပဲ |

> `git restore filename.txt` က file ကို `git add` လုပ်ထားပြီးသားဆိုရင် **staged version** အတိုင်းပဲ ပြန်ဖြစ်မယ်၊ နောက်ဆုံး commit အတိုင်း မဟုတ်ဘူး။
>
> `git checkout HEAD filename.txt` နဲ့ အတိအကျ တူချင်ရင်:
> ```bash
> git restore --source=HEAD --staged --worktree filename.txt
> ```

---

## ၅။ File တစ်ခုကို Commit ဟောင်းအတိုင်း ပြန်ထားခြင်း

```bash
git restore --source HEAD~1 filename.txt
```

`filename.txt` ကို နောက်ဆုံးမတိုင်ခင် commit (`HEAD~1`) မှာ ရှိခဲ့တဲ့ ပုံစံအတိုင်း ပြန်ထားပါတယ်။ ဘယ် commit ကိုမဆို သုံးလို့ရပါတယ်။

```bash
git restore --source 604a39a filename.txt
```

> ဒီ command က **working directory ကိုပဲ** ပြောင်းပါတယ်။ ဒီပြောင်းလဲမှုကို သိမ်းချင်ရင် `git add` နဲ့ `git commit` ထပ်လုပ်ရပါမယ်။

---

## ၆။ File ကို Unstage လုပ်ခြင်း

```bash
git restore --staged filename.txt
```

File ထဲက ပြင်ဆင်ချက်တွေ မပျောက်ပါဘူး၊ staging area ကနေပဲ ဖယ်ထုတ်တာပါ။

---

## ၇။ Repository ကို Commit ဟောင်းဆီ Reset လုပ်ခြင်း

`git reset` က `HEAD` နဲ့ လက်ရှိ branch ကို commit ဟောင်းဆီ ရွှေ့ပါတယ်။ အဲဒီနောက်က commit တွေ ဘာဖြစ်မလဲဆိုတာ mode ပေါ်မူတည်ပါတယ်။

```bash
git reset --soft 604a39a    # Commit ပျောက်၊ ပြောင်းလဲမှုတွေ staged အနေနဲ့ ကျန်
git reset 604a39a           # Commit ပျောက်၊ ပြောင်းလဲမှုတွေ unstaged အနေနဲ့ ကျန် (default = --mixed)
git reset --hard 604a39a    # Commit ရော ပြောင်းလဲမှုတွေပါ ပျောက်
```

| Mode | Commit | Staging Area | Working Directory |
|---|---|---|---|
| `--soft` | ပျောက် | ✅ ကျန် | ✅ ကျန် |
| `--mixed` (default) | ပျောက် | ❌ ရှင်း | ✅ ကျန် |
| `--hard` | ပျောက် | ❌ ရှင်း | ❌ ရှင်း |

> ⚠️ Push ပြီးသား branch ကို reset လုပ်ရင် history ပြောင်းသွားလို့ force push လိုပါတယ်။ Share ထားတဲ့ branch ဆိုရင် `git revert` ကို သုံးပါ။

---

## ၈။ Commit ကို Revert လုပ်ခြင်း

```bash
git revert 604a39a
```

Commit `604a39a` ရဲ့ ပြောင်းလဲမှုတွေကို ပြန်ဖျက်ပေးတဲ့ **commit အသစ်တစ်ခု** ဖန်တီးပါတယ်။ History ဟောင်းကို မဖျက်ပါဘူး။

```
Revert မလုပ်ခင်:   A---B---C
Revert လုပ်ပြီး:    A---B---C---C̄   (C̄ = C ကို ပြန်ဖျက်တဲ့ commit)
```

| | `git reset` | `git revert` |
|---|---|---|
| History | ပြောင်းတယ် (commit ဖျက်) | မပြောင်း (commit အသစ်ထည့်) |
| Shared branch မှာ | ❌ မသုံးသင့် | ✅ Safe |
| Force push | လိုတယ် | မလို |

---

## Quick Reference

| လုပ်ချင်တာ | Command |
|---|---|
| Commit ဟောင်းကို ကြည့်ရုံ | `git switch --detach <sha>` |
| Branch ဆီ ပြန်လာ | `git switch main` |
| Commit ဟောင်းကနေ branch အသစ် | `git switch -c <branch> <sha>` |
| Remote အတိုင်း အကုန်ပြန်ထား | `git reset --hard origin/main` |
| File ကို နောက်ဆုံး commit အတိုင်း | `git restore --source=HEAD --staged --worktree <file>` |
| File ကို commit ဟောင်းအတိုင်း | `git restore --source <sha> <file>` |
| File ကို unstage | `git restore --staged <file>` |
| Local commit ပြန်ဖျက် | `git reset [--soft \| --mixed \| --hard] <sha>` |
| Shared commit ပြန်ဖျက် | `git revert <sha>` |

---

## Reference

- [Git Cheatsheet (PDF) – eshantrivedi21](https://eshantrivedi21.github.io/Git-CheatSheet/PDF/Cheatsheet_PDF.pdf)
