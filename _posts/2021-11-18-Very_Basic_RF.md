---
layout:            post
title:             "Very Basic RF"
date:              2021-11-18
tags:              RF
category:          Others
author:            hongyuan

---

## Amplitude, Power & Decibels

For amplitude: dB = 20 * log10(v1/v2).

For power: dB = 10 * log10(p1/p2).

Most of the time we deal with power, so dB = 10 * log10(p1/p2).

Conversion between dB and linear, or between dBm and mWatt:

```python
def linear_to_db(linear):
    db = 10.0 * np.log10(linear)
    return db

def db_to_linear(db):
    linear = 10.0 ** (db / 10.0)
    return linear

def dbm_to_mwatt(dbm):
    mwatt = 10.0 ** (dbm / 10.0)
    return mwatt

def mwatt_to_dbm(mwatt):
    dbm = 10.0 * np.log10(mwatt)
    return dbm

print(db_to_linear(3))  # 3dB -> 2
print(linear_to_db(10)) # 10 -> 10dB
print(dbm_to_mwatt(20)) # 20dBm -> 100mW
print(mwatt_to_dbm(1))  # 1mW -> 0dBm
```

