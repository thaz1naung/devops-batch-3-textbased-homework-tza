# Git Rebase vs Merge — Cheatsheet

1. [အခြေခံ သဘောတရား](#၁-အခြေခံ-သဘောတရား)
2. [အဓိက နှိုင်းယှဉ်ချက်](#၂-အဓိက-နှိုင်းယှဉ်ချက်)
3. [Rebase ရဲ့ Golden Rule](#၃-rebase-ရဲ့-golden-rule)
4. [Interactive Rebase (-i)](#၄-interactive-rebase--i)
5. [Workflow အဆင့်လိုက် ဘာသုံးမလဲ](#၅-workflow-အဆင့်လိုက်-ဘာသုံးမလဲ)
6. [Force Push](#၆-force-push)
7. [ဘေးကင်းစွာ Rebase လုပ်နည်းများ](#၇-ဘေးကင်းစွာ-rebase-လုပ်နည်းများ)
8. [အကျဉ်းချုပ် — ဘာသုံးမလဲ](#၈-အကျဉ်းချုပ်--ဘာသုံးမလဲ)

---

## ၁။ အခြေခံ သဘောတရား

နှစ်ခုလုံးက branch တစ်ခုရဲ့ ပြောင်းလဲမှုကို တခြား branch ထဲ ပေါင်းထည့်ဖို့ ဖြစ်ပါတယ်။ **ပေါင်းပုံပဲ ကွာပါတယ်။**

**စမှတ်:** `feature` ကို ခွဲထုတ်ပြီးနောက် တခြားသူက `main` ကို commit အသစ်တွေ ထည့်လိုက်တယ်။

```
main:      A---B---E---F
                \
feature:         C---D
```

### Merge Result

```bash
git checkout feature
git merge main
```

```
main:      A---B---E---F
                \       \
feature:         C---D---M     ← Merge commit အသစ်
```

### Rebase Result

```bash
git checkout feature
git rebase main
```

```
main:      A---B---E---F
                        \
feature:                 C'---D'   ← Commit အသစ်များ (hash ပြောင်း)
```

---

## ၂။ အဓိက နှိုင်းယှဉ်ချက်

| | Merge | Rebase |
|---|---|---|
| **လုပ်ဆောင်ပုံ** | Merge commit အသစ် ဖန်တီးပြီး history နှစ်ခု ချိတ် | Feature commit တွေကို main ရဲ့ ထိပ်မှာ အသစ်ပြန်ဖန်တီး |
| **History** | Branch ပုံ (fork) ပေါ် | Linear (တစ်ကြောင်းတည်း) |
| **History ပြန်ရေးလား** | ❌ မရေး | ✅ ပြန်ရေး |
| **ဘေးကင်းမှု** | ✅ အရမ်းဘေးကင်း | ⚠️ Golden Rule မလိုက်နာရင် အန္တရာယ်ရှိ |
| **Traceability** | main ကို ဘယ်တုန်းက ပေါင်းခဲ့လဲ သိရ | ဘယ်တုန်းက ပေါင်းခဲ့လဲ မသိရ |
| **အားနည်းချက်** | main active ဖြစ်ရင် merge commit တွေ များပြီး history ရှုပ် | Share branch မှာ သုံးရင် team ကို ပြဿနာ ဖြစ်စေ |
| **`git log`, `git bisect`** | ဖတ်ရ ခက်နိုင် | ဖတ်ရ လွယ် |
| **Conflict ဖြေရှင်းခြင်း** | တစ်ကြိမ်တည်း | Commit တစ်ခုချင်းစီမှာ ဖြစ်နိုင် |

---

## ၃။ Rebase ရဲ့ Golden Rule

> ### ⚠️ Public branch (တခြားသူတွေ သုံးနေတဲ့ branch) ကို ဘယ်တော့မှ rebase မလုပ်ပါနဲ့။
>
> Rebase က commit hash တွေကို ပြောင်းလိုက်လို့ တခြားသူတွေရဲ့ local history နဲ့ မကိုက်တော့ဘဲ duplicate commit တွေ၊ conflict တွေ ဖြစ်လာပါတယ်။

---

## ၄။ Interactive Rebase (`-i`)

Commit တွေကို ရွှေ့ရင်း ပြင်ဆင်လို့ရတဲ့ နည်းပါ။ Merge နဲ့ ဒါမျိုး လုပ်လို့ မရပါဘူး။

```bash
git rebase -i main        # main နောက်ပိုင်း commit အားလုံးကို ပြင်
git rebase -i HEAD~3      # နောက်ဆုံး commit ၃ ခုကိုပဲ ပြင် (branch ရဲ့ base ကို မရွှေ့)
```

Editor ပွင့်လာရင် commit တစ်ခုချင်းရှေ့က keyword ကို ပြောင်းပါ:

```
pick   a1b2c3d Add login form
squash e4f5g6h Add tests
fixup  i7j8k9l Fix typo
```

| Keyword | အဓိပ္ပာယ် |
|---|---|
| `pick` (`p`) | Commit ကို ဒီအတိုင်း ထား |
| `reword` (`r`) | Commit message ကိုပဲ ပြင် |
| `edit` (`e`) | Commit မှာ ရပ်ပြီး content ကို ပြင်ခွင့်ပေး |
| `squash` (`s`) | အရင် commit နဲ့ ပေါင်း၊ message နှစ်ခုလုံး ပေါင်းရေး |
| `fixup` (`f`) | အရင် commit နဲ့ ပေါင်း၊ ဒီ commit ရဲ့ message ကို ပစ် |
| `drop` (`d`) | Commit ကို ဖျက် |

> Line တွေရဲ့ အစီအစဉ်ကို ရွှေ့ရင် commit အစီအစဉ်လည်း ပြောင်းပါတယ်။

---

## ၅။ Workflow အဆင့်လိုက် ဘာသုံးမလဲ

| အဆင့် | Branch အခြေအနေ | သုံးရမယ့်နည်း | Command |
|---|---|---|---|
| ① Local cleanup | Private (ကိုယ်တစ်ယောက်တည်း) | Interactive rebase | `git rebase -i HEAD~3` |
| ② main ကနေ update ယူ | Private | Rebase (သို့) Merge | `git rebase main` |
| ③ Teammate နဲ့ feature branch တစ်ခုတည်း မျှသုံး | ကိုယ့် local commit တွေကိုပဲ ရွှေ့ရင် | Rebase | `git pull --rebase` |
| ④ Pull Request ဖွင့်ပြီးနောက် | Public (reviewer တွေ ကြည့်နေ) | Rebase မလုပ်ရ၊ Merge ရ | `git merge main` |
| ⑤ Approve ရပြီး main ထဲ ပေါင်း | main က public | Rebase လုပ်ပြီးမှ merge (fast-forward) | အောက်တွင်ကြည့်ပါ |

**⑤ အတွက် Command အပြည့်:**

```bash
git checkout feature
git rebase main
git checkout main
git merge --ff-only feature
```

> ③ မှာ `git pull --rebase` က push မလုပ်ရသေးတဲ့ **ကိုယ့် local commit** တွေကိုပဲ remote commit တွေရဲ့ ထိပ်ကို ရွှေ့တာမို့ Golden Rule ကို မချိုးဖောက်ပါဘူး။

---

## ၆။ Force Push

Push ပြီးသား branch ကို rebase လုပ်ရင် remote က push ကို ငြင်းပယ်ပါတယ်။ Force push လုပ်ရပါမယ်။

| Command | အဓိပ္ပာယ် |
|---|---|
| `git push --force-with-lease` | ✅ ဘေးကင်း — remote မှာ သူများ commit ရှိရင် ရပ် |
| `git push --force` | ⚠️ အန္တရာယ်ရှိ — အကုန် overwrite |

---

## ၇။ ဘေးကင်းစွာ Rebase လုပ်နည်းများ

### Temporary branch မှာ စမ်းပါ

မှားရင် မူလ `feature` branch ကို ပြန်သုံးလို့ရပါတယ်။

```bash
git checkout feature
git checkout -b temp-branch
git rebase -i main
# ... history ကို သန့်ရှင်းအောင် ပြင် ...
git checkout main
git merge temp-branch
```

### Conflict ဖြစ်ရင်

```bash
git status                 # ဘယ် file conflict ဖြစ်လဲ ကြည့်
# file ကို ပြင်ပြီး
git add <file>
git rebase --continue      # ဆက်လုပ်
```

အခြား ရွေးချယ်စရာများ:

| Command | အဓိပ္ပာယ် |
|---|---|
| `git rebase --continue` | Conflict ရှင်းပြီးနောက် ဆက်လုပ် |
| `git rebase --skip` | ဒီ commit ကို ကျော် |
| `git rebase --abort` | Rebase လုံးဝ ဖျက်သိမ်း၊ မူလအတိုင်း ပြန်ထား |

### မှားသွားရင်

```bash
git reflog                       # Rebase မလုပ်ခင် commit ကို ရှာ
git reset --hard <old-sha>
# (သို့)
git reset --hard ORIG_HEAD       # Rebase မလုပ်ခင် နေရာကို တိုက်ရိုက် ပြန်သွား
```

> `ORIG_HEAD` က rebase / merge / reset လို command တွေ မလုပ်ခင် `HEAD` ရှိခဲ့တဲ့ နေရာကို မှတ်ထားပါတယ်။ နောက်ထပ် အဲဒီလို command တစ်ခု လုပ်လိုက်ရင် overwrite ဖြစ်သွားလို့ ချက်ချင်း သုံးပါ။

---

## ၈။ အကျဉ်းချုပ် — ဘာသုံးမလဲ

```
Branch ကို တခြားသူ သုံးနေလား (push ပြီး share ထားလား / PR ဖွင့်ထားလား)?
├─ ဟုတ်တယ် → MERGE  (history မပြင်ရ)
└─ မဟုတ်ဘူး
    ├─ Linear, သန့်ရှင်းတဲ့ history လိုချင်တယ်               → REBASE
    ├─ Commit အသေးတွေ ပေါင်း/ပြင်/ဖျက်ချင်တယ်               → INTERACTIVE REBASE
    └─ History အပြည့်အစုံ ထိန်းချင်တယ်၊ risk မယူချင်ဘူး        → MERGE
```

---

## Reference

- [Merging vs. Rebasing – Atlassian Git Tutorial](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)
