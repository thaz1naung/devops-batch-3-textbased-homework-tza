# Git Branching Cheatsheet

1. [Branch ဖန်တီးခြင်း](#၁-branch-ဖန်တီးခြင်း)
2. [Remote Branch မှ ဆွဲယူခြင်း](#၂-remote-branch-မှ-ဆွဲယူခြင်း)
3. [Branch များကို ကြည့်ခြင်း](#၃-branch-များကို-ကြည့်ခြင်း)
4. [Branch ဖျက်ခြင်း](#၄-branch-ဖျက်ခြင်း)
5. [လက်ရှိ Commit ရဲ့ SHA ကြည့်ခြင်း](#၅-လက်ရှိ-commit-ရဲ့-sha-ကြည့်ခြင်း)
6. [Reset လုပ်ခြင်း](#၆-reset-လုပ်ခြင်း)

---

## ၁။ Branch ဖန်တီးခြင်း

```bash
git checkout -b feature-login
git push origin feature-login --set-upstream
```

**သို့မဟုတ်** (Git 2.23+ မှာ `checkout` အစား `switch` သုံးတာ ပိုရှင်းတယ်)

```bash
git switch -c feature-login
git push -u origin feature-login   # -u = --set-upstream
```

- `checkout -b` / `switch -c` က branch အသစ် ဖန်တီးပြီး အဲဒီ branch ဆီ ချက်ချင်း ပြောင်းပေးတယ်။
- `--set-upstream` (`-u`) က local branch ကို remote branch နဲ့ ချိတ်ပေးတယ်။ ဒါမှ နောက်ပိုင်း `git push` / `git pull` ကို branch နာမည် မထည့်ဘဲ ရိုက်လို့ရမယ်။

---

## ၂။ Remote Branch မှ ဆွဲယူခြင်း

```bash
git fetch origin
git switch feature-login
```

> Local မှာ `feature-login` မရှိသေးပေမယ့် `origin/feature-login` ရှိနေရင် `git switch` က remote branch ကို track လုပ်တဲ့ local branch ကို အလိုအလျောက် ဖန်တီးပေးတယ်။

---

## ၃။ Branch များကို ကြည့်ခြင်း

| Command | အဓိပ္ပာယ် |
|---|---|
| `git branch --list` | Local branch အားလုံး (လက်ရှိ branch မှာ `*` ပြ) |
| `git branch -a --merged` | လက်ရှိ branch ထဲ merge ပြီးသား branch များ (local + remote) |

---

## ၄။ Branch ဖျက်ခြင်း

### Local branch ဖျက်ခြင်း

| Command | အဓိပ္ပာယ် |
|---|---|
| `git branch -d feature-login` | Safe ဖြစ်တဲ့ ဖျက်နည်း (merge မလုပ်ရသေးရင် ဖျက်ခွင့်မပေး) |
| `git branch -D feature-login` | အတင်းဖျက် (`--delete --force`) |

### Remote branch ဖျက်ခြင်း

```bash
git push origin --delete feature-login
git push origin :feature-login          # syntax အဟောင်း၊ အလုပ်လုပ်ပုံ အတူတူ
```

> Tag တွေကိုလည်း ဒီနည်းအတိုင်း ဖျက်လို့ရပါတယ် — `git push origin --delete v1.0`

### Remote-tracking branch ဟောင်းတွေကို ရှင်းခြင်း

```bash
git remote prune origin
```

> Remote မှာ ဖျက်ပြီးသားဖြစ်ပေမယ့် local မှာ ကျန်နေတဲ့ `origin/...` reference တွေကို ရှင်းပေးတယ်။

---

## ၅။ လက်ရှိ Commit ရဲ့ SHA ကြည့်ခြင်း

| Command | အဓိပ္ပာယ် |
|---|---|
| `git rev-parse HEAD` | SHA အပြည့် |
| `git rev-parse --short HEAD` | SHA အတို (ဥပမာ `9f8e7d6`) |

---

## ၆။ Reset လုပ်ခြင်း

### Uncommitted ပြောင်းလဲမှု အားလုံး ဖျက်ခြင်း

```bash
git reset --hard
```

> ⚠️ Commit မလုပ်ရသေးတဲ့ ပြင်ဆင်ချက်အားလုံး (staged ရော unstaged ပါ) ပြန်ယူလို့မရအောင် ပျက်မှာပါ။

### Commit အဟောင်းတစ်ခုဆီ ပြန်သွားခြင်း

```bash
git reset --hard 1a2b3c4
```

Push ပြီးသား branch ဆိုရင် remote ကို update လုပ်ဖို့ force push လိုပါတယ်။

| Command | အဓိပ္ပာယ် |
|---|---|
| `git push --force-with-lease` | ✅ Safe ဖြစ်တဲ့ force push — remote မှာ တခြားသူ push ထားတာရှိရင် ရပ်ပေးတယ် |
| `git push --force` | ⚠️ Dangerous — remote ပေါ်က တခြားသူ့ commit တွေကို overwrite လုပ်နိုင်တယ် |

---

## Reference

- [devhints.io – Git branches](https://devhints.io/git-branch)
