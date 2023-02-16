# Introduction

If you are using WiX Toolset v3 with VS 2022, you properly face this problem, that VS always crash during the compiling process.

To fix this issue, you have to add the attribute `RunAsSeparateProcess=true` into the `HeatDirectory` tasks.

---

*Source: https://github.com/wixtoolset/issues/issues/6636#issuecomment-984886136*