# Different Types of Merge Strategies in Git

1. [Fast Forward Merge](#1-fast-forward-merge)
2. [Recursive Merge](#2-recursive-merge)
3. [Octopus Merge](#3-octopus-merge)
4. [Subtree Merge](#4-subtree-merge)
5. [Squash and Merge](#5-squash-and-merge)
6. [Rebase and Merge](#6-rebase-and-merge)

---

## 1. Fast Forward Merge

Fast-forward merge ဆိုတာ target branch (`main`) မှာ source branch (eg. `feature`) ကို ခွဲထုတ်ပြီးကတည်းက commit အသစ် တစ်ခုမှ မထပ်ရှိသေးတဲ့အခါ ဖြစ်တဲ့ merge အမျိုးအစား ဖြစ်တယ်။

- **main မှာ commit အသစ် မရှိမှ ဖြစ်နိုင်တယ်:** feature ကို ခွဲထုတ်ပြီးနောက် main ပေါ်မှာ ဘယ်သူမှ commit မထည့်ရသေးရင်ပဲ fast-forward လုပ်လို့ရတယ်။
- **Branch pointer ကိုပဲ ရွှေ့တယ်:** commit အသစ် မဖန်တီး၊ file တွေကို ပေါင်းစပ်ဖို့ မလိုဘူး။
- `git log` ကြည့်တဲ့အခါ ရှုပ်ထွေးတဲ့ merge လမ်းကြောင်းတွေ မရှိဘဲ ဖတ်ရလွယ်တယ်။

### ဥပမာ

Merge မလုပ်ခင် `main` က commit `B` မှာ ရှိနေတယ်။ `feature` branch ကို `B` ကနေ ခွဲထုတ်ပြီး commit `C` နဲ့ `D` ထည့်ထားတယ်။ `main` ပေါ်မှာတော့ commit အသစ် မရှိသေးဘူး။

```
main:      A---B
                \
feature:         C---D
```

Merge လုပ်တဲ့ command

```bash
git checkout main
git merge feature
```

Merge လုပ်ပြီးနောက် `main` pointer က `B` ကနေ `D` ဆီကို ရွှေ့သွားတယ်။ Merge commit အသစ် မရှိပါဘူး။

```
main, feature:   A---B---C---D
```

> **သတိပြုရန်:** အကယ်၍ feature ကို ခွဲထုတ်ပြီးနောက် တခြားသူက `main` ပေါ်မှာ commit `E` ထည့်လိုက်ရင် branch နှစ်ခု **diverge** ဖြစ်သွားပါပြီ။ ဒီအခါ pointer ကို ရွှေ့ရုံနဲ့ မရတော့ပါဘူး။ Git က 3-way merge လုပ်ပြီး merge commit `M` အသစ်တစ်ခု ဖန်တီးပါတယ်။

### အသုံးဝင်တဲ့ Option များ

| Command | အဓိပ္ပာယ် |
|---|---|
| `git merge feature` | ဖြစ်နိုင်ရင် fast-forward လုပ်တယ်၊ မဖြစ်နိုင်ရင် merge commit ဖန်တီးတယ် (default) |
| `git merge --ff-only feature` | Fast-forward ပဲ လုပ်မယ်။ မဖြစ်နိုင်ရင် error ပြပြီး ရပ်လိုက်တယ် |
| `git merge --no-ff feature` | Fast-forward ဖြစ်နိုင်ရင်တောင် merge commit ကို အတင်း ဖန်တီးတယ် |

> **P.S.** `--no-ff` ကို Git Flow မှာ အသုံးများပါတယ်။

---

## 2. Recursive Merge

Recursive merge ဆိုတာ branch နှစ်ခု diverge ဖြစ်သွားတဲ့အခါ Git က အသုံးပြုတဲ့ merge strategy ဖြစ်တယ်။ Branch နှစ်ခုလုံးမှာ ပြောင်းလဲမှုတွေကို ပေါင်းစပ်ပြီး merge commit အသစ်တစ်ခု ဖန်တီးပေးတယ်။

- **Branch နှစ်ခုလုံးမှာ commit အသစ်ရှိမှ သုံးတယ်** (feature ကို ခွဲထုတ်ပြီးနောက် main မှာရော feature မှာရော commit အသစ်တွေ ရှိနေတဲ့အခါ)

### ဥပမာ

Merge မလုပ်ခင် `feature` ကို commit `B` ကနေ ခွဲထုတ်ခဲ့တယ်။ ပြီးနောက် `main` မှာ `E` ထည့်ပြီး `feature` မှာ `C`, `D` ထည့်ခဲ့တယ်။

```
main:      A---B---E
                \
feature:         C---D
```

Merge လုပ်တဲ့ command

```bash
git checkout main
git merge feature
```

Merge လုပ်ပြီးနောက် Git က merge commit `M` ကို ဖန်တီးတယ်။ `M` ရဲ့ parent နှစ်ခုက `E` နဲ့ `D` ဖြစ်ပါတယ်။

```
main:      A---B---E-------M
                \         /
feature:         C-------D
```

> **Merge Conflict:** Branch နှစ်ခုမှာ file တစ်ခုတည်းရဲ့ line တစ်ခုတည်းကို မတူအောင် ပြင်ထားရင် merge conflict ဖြစ်တယ်။ Manual ရှင်းပြီးမှ `git add` နဲ့ `git commit` လုပ်ရပါတယ်။

### အသုံးဝင်တဲ့ Option များ

| Command | အဓိပ္ပာယ် |
|---|---|
| `git merge feature` | Diverge ဖြစ်ရင် merge commit ဖန်တီးတယ် |
| `git merge --no-ff feature` | Fast-forward ဖြစ်နိုင်ရင်တောင် merge commit ကို အတင်း ဖန်တီးတယ် |

---

## 3. Octopus Merge

Branch နှစ်ခုထက် ပိုများတဲ့ branch တွေကို တစ်ပြိုင်နက်တည်း merge လုပ်တဲ့ strategy ဖြစ်တယ်။ Branch တစ်ခုချင်းစီကို တစ်ခါပြီးတစ်ခါ merge လုပ်မယ့်အစား command တစ်ခုတည်းနဲ့ branch အားလုံးကို ပေါင်းပြီး merge commit တစ်ခုတည်း ဖန်တီးပေးပါတယ်။

- **Branch နှစ်ခုထက် ပိုပြီး တစ်ခါတည်း merge လုပ်တယ်:** Merge commit မှာ parent သုံးခု၊ လေးခု စသဖြင့် ရှိနိုင်တယ်။
- **Conflict ရှိရင် လုံးဝ မလုပ်ဘူး:** Manual conflict resolution လိုအပ်မယ့် ရှုပ်ထွေးတဲ့ merge ဆိုရင် octopus က merge ကို ရပ်လိုက်တယ်။ ဒါကြောင့် conflict မဖြစ်တဲ့ (file မတူတာ ပြင်ထားတဲ့) branch တွေအတွက်ပဲ သင့်တော်ပါတယ်။

### ဥပမာ

Merge မလုပ်ခင် `main` ကနေ feature branch သုံးခု ခွဲထုတ်ထားတယ်။ တစ်ခုစီက file မတူတာတွေကို ပြင်ထားတယ်။

```
              C        (feature-a: login.js)
             /
main:   A---B---D      (feature-b: header.css)
             \
              E        (feature-c: README.md)
```

Merge လုပ်တဲ့ command

```bash
git checkout main
git merge feature-a feature-b feature-c
```

Merge လုပ်ပြီးနောက် merge commit `M` တစ်ခုတည်း ဖန်တီးပြီး parent လေးခု (`B`, `C`, `D`, `E`) ရှိပါတယ်။

```
              C
             / \
main:   A---B---D---M
             \     /
              E---/
```

> Conflict ဖြစ်ရင် `Merge with strategy octopus failed.` error ပြပြီး ရပ်သွားမယ်။

---

## 4. Subtree Merge

Project တစ်ခုလုံးကို တခြား project ရဲ့ subdirectory တစ်ခုထဲကို merge လုပ်တဲ့ strategy ဖြစ်တယ်။

- **Project တစ်ခုလုံးကို folder ခွဲထဲ ထည့်တယ်:** ပြင်ပ repository ရဲ့ root က file တွေဟာ ကိုယ့် repo ရဲ့ subdirectory အောက်ကို ရောက်သွားတယ်။
- **History ကို သိမ်းထားတယ်:** Copy-paste လုပ်တာနဲ့ မတူဘဲ ပြင်ပ project ရဲ့ commit history ကို သိမ်းထားတယ်။
- **Clone လုပ်ရုံနဲ့ အကုန်ရ:** Code အားလုံး repository ထဲမှာ တကယ် ရှိနေလို့ `git clone` လုပ်ရုံနဲ့ အကုန်ရ။ အပိုလုပ်ဆောင်ချက် မလို။

### ဥပမာ

`my-app` project ထဲကို ပြင်ပ `lib` project ကို `vendor/lib/` folder အောက်မှာ ထည့်ချင်တယ်။

```
Merge မလုပ်ခင်:                  Merge လုပ်ပြီးနောက်:

my-app/                        my-app/
├── src/                       ├── src/
└── README.md                  ├── README.md
                               └── vendor/
lib/ (ပြင်ပ repo)                   └── lib/
├── utils.js                           ├── utils.js
└── index.js                           └── index.js
```

### ပထမဆုံးအကြိမ် ထည့်ရင်

```bash
# ၁။ ပြင်ပ repo ကို remote အဖြစ် ထည့်ပြီး fetch လုပ်
git remote add lib https://github.com/example/lib.git
git fetch lib

# ၂။ History ကို ချိတ်ဆက်ဖို့ merge စတင် (file တွေ မပြောင်းသေး)
git merge -s ours --no-commit --allow-unrelated-histories lib/main

# ၃။ lib ရဲ့ file တွေကို vendor/lib/ အောက်ကို ထည့်
git read-tree --prefix=vendor/lib/ -u lib/main

# ၄။ Commit လုပ်
git commit -m "Add lib as subtree in vendor/lib"
```

### နောက်ပိုင်း Update ဆွဲရင် (lib project မှာ update အသစ်ထွက်လာရင်)

```bash
git fetch lib
git merge -s subtree lib/main
```

---

## 5. Squash and Merge

Feature branch ပေါ်က commit အားလုံးကို commit တစ်ခုတည်းအဖြစ် ချုံ့ပြီး target branch (eg. `main`) ပေါ်ကို ထည့်တဲ့ နည်းလမ်း ဖြစ်ပါတယ်။ Merge commit မဖန်တီးပါဘူး။

- Feature branch မှာ commit ၁၀ ခု ရှိရင်လည်း `main` မှာ commit ၁ ခုပဲ ရှိမယ်။
- ရလဒ်က parent တစ်ခုပဲရှိတဲ့ ပုံမှန် commit ဖြစ်တယ်။ ဒါကြောင့် history က **linear** ဖြစ်တယ်။
- Feature တစ်ခု = commit တစ်ခု ဖြစ်လို့ `git log` ဖတ်ရလွယ်ပြီး feature တစ်ခုလုံးကို `git revert` နဲ့ ပြန်ဖျက်ရလည်း လွယ်တယ်။

### ဥပမာ

`feature` branch မှာ commit သုံးခု (`C`, `D`, `E`) ရှိတယ်။

```
main:      A---B
                \
feature:         C---D---E
                 │   │   └─ "Fix typo"
                 │   └───── "Add tests"
                 └───────── "Add login form"
```

Merge လုပ်တဲ့ command

```bash
git checkout main
git merge --squash feature
git commit -m "Add login feature"
```

Merge လုပ်ပြီးနောက် `C`, `D`, `E` ရဲ့ ပြောင်းလဲမှုအားလုံးကို ပေါင်းထားတဲ့ commit `S` တစ်ခုတည်း `main` ပေါ်မှာ ရှိပါတယ်။

```
main:      A---B---S   ("Add login feature")
```

---

## 6. Rebase and Merge

Feature branch ကို ခွဲထုတ်ခဲ့တဲ့ အစမှတ် (base) ကို base branch ရဲ့ နောက်ဆုံး commit ဆီ ရွှေ့ပြီး feature commit တွေကို အဲဒီ commit အပေါ်မှာ တစ်ခုချင်း ပြန်ထည့် (reapply) တဲ့ နည်းလမ်း ဖြစ်ပါတယ်။ ပြီးမှ fast-forward merge လုပ်ပါတယ်။

Fast-forward merge နဲ့ ဆင်တူပါတယ်။ ကွာခြားချက်က fast-forward က `main` မှာ commit အသစ် မရှိမှ လုပ်လို့ရပေမယ့် rebase and merge ကတော့ `main` မှာ commit အသစ်တွေ ဝင်လာပြီးသားဖြစ်နေရင်တောင် linear history ရအောင် လုပ်ပေးနိုင်ပါတယ်။

### ဥပမာ

`feature` ကို `B` ကနေ ခွဲထုတ်ခဲ့တယ်။ နောက်ပိုင်း `main` မှာ `E` ဝင်လာတယ်။ Branch နှစ်ခု diverge ဖြစ်နေပြီ။

```
main:      A---B---E
                \
feature:         C---D
```

**အဆင့် ၁: Feature branch ကို rebase လုပ်**

```bash
git checkout feature
git rebase main
```

Git က `C` နဲ့ `D` ကို `E` အပေါ်မှာ ပြန်ထည့်ပါတယ်။ ရလဒ်က commit အသစ် `C'` နဲ့ `D'` ဖြစ်ပါတယ်။

```
main:      A---B---E
                    \
feature:             C'---D'
```

**အဆင့် ၂: Main ကို fast-forward merge လုပ်**

```bash
git checkout main
git merge --ff-only feature
```

```
main, feature:   A---B---E---C'---D'
```

Merge commit မရှိဘဲ history တစ်ကြောင်းတည်းပါ။ Feature ရဲ့ commit တစ်ခုချင်းစီ (`C'`, `D'`) လည်း ကျန်နေပါသေးတယ်။

**အားသာချက်:** Squash လို တစ်ခုတည်း မချုံ့ဘဲ commit အသေးစိတ်တွေ ကျန်နေလို့ `git bisect` နဲ့ bug ရှာရ လွယ်တယ်၊ ပြောင်းခဲ့တဲ့ commit တစ်ခုချင်း ကြည့်လို့ရတယ်။

**အားနည်းချက်:** ဘယ် commit က ဘယ် PR / branch ကလဲ ခွဲခြားရ ခက်တယ်။

> ### ⚠️ Rebase ရဲ့ Golden Rule
> တခြားသူတွေ သုံးနေတဲ့ (push ပြီးသား၊ share လုပ်ထားတဲ့) branch ကို rebase မလုပ်ပါနဲ့။ ကိုယ်တစ်ယောက်တည်း သုံးနေတဲ့ feature branch မှာပဲ rebase လုပ်ပါ။

---

## References

- [Merge Strategies in Git – GeeksforGeeks](https://www.geeksforgeeks.org/git/merge-strategies-in-git/)
- [Different Merge Types in Git – Luke Merrett](https://lukemerrett.com/different-merge-types-in-git/)
