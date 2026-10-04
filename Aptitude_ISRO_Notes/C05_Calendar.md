# C05. Calendar

> Only one idea matters: **odd days**, the days left over after removing whole weeks. Count odd days, take the remainder mod 7, and read off the weekday. Every calendar question is a variation of this.

---

## 1. Odd days

- Ordinary year: 365 = 52 weeks + **1** odd day.
- Leap year: 366 = 52 weeks + **2** odd days.
- So each year moves a date's weekday forward by 1 (or 2 if the year contains 29 February).

### Month odd days (non-leap year)

| Jan | Feb | Mar | Apr | May | Jun | Jul | Aug | Sep | Oct | Nov | Dec |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 3 | 0 | 3 | 2 | 3 | 2 | 3 | 3 | 2 | 3 | 2 | 3 |

(31 days = 3 odd, 30 = 2, 28 = 0, 29 = 1.)

### Weekday codes

| 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|
| Sun | Mon | Tue | Wed | Thu | Fri | Sat |

## 2. Leap years

Divisible by 4 → leap, **except** century years, which must be divisible by **400**.
- 2000, 2400: leap. 1900, 1800, 2100: **not** leap.

## 3. Centuries

| Span | Odd days |
|---|---|
| 100 years | 5 |
| 200 years | 3 |
| 300 years | 1 |
| 400 years | **0** |

(100 years = 76 ordinary + 24 leap = 76 + 48 = 124 → 124 mod 7 = 5.)

**Consequence:** the last day of a century can only be **Friday, Wednesday, Monday or Sunday** (codes 5, 3, 1, 0). Never Tuesday, Thursday or Saturday.

## 4. Day of the week for any date

```
Total odd days = odd days of all complete years before it
               + odd days of complete months before it in that year
               + the date
Weekday = total mod 7
```

**15 August 1947:**
- 1946 complete years = 1900 (1 odd day: 1600 → 0, 300 → 1) + 46 years with 11 leap years (46 + 11 = 57 → 1). Total 2.
- Jan to Jul 1947: 3 + 0 + 3 + 2 + 3 + 2 + 3 = 16 → 2.
- Date 15 → 1.
- 2 + 2 + 1 = 5 → **Friday**.

**26 January 1950:** 1900 → 1; 1901 to 1949: 49 years, 12 leap → 61 → 5; date 26 → 5. Total 11 → 4 → **Thursday**.

**1 January 1901:** 1900 years → 1, plus 1 → 2 → **Tuesday**.

### Relative counting (faster in practice)

- Monday + 61 days: 61 mod 7 = 5 → **Saturday**.
- Wednesday + 100 days: 100 mod 7 = 2 → **Friday**.
- 1 Jan 2023 was Sunday → 1 Jan 2024 is Monday (2023 has 1 odd day) → 1 Jan 2025 is **Wednesday** (2024 is leap: 2 odd days).
- Going backwards over a 29 February subtracts 2: 8 Feb 2005 was Tuesday → 8 Feb 2004 was **Sunday** (the gap contains 29 Feb 2004).

> A non-leap year **starts and ends on the same weekday** (364 days between 1 Jan and 31 Dec).

## 5. Months with the same calendar

**Non-leap year:** Jan = Oct; Feb = Mar = Nov; Apr = Jul; Sep = Dec.
**Leap year:** Jan = Apr = Jul; Feb = Aug; Mar = Nov; Sep = Dec.

Reason: the odd days between their 1sts add to a multiple of 7. (Non-leap Jan to Oct: 3 + 0 + 3 + 2 + 3 + 2 + 3 + 3 + 2 = 21.)

## 6. Repeating year calendars

Two years have identical calendars when (1) the odd days between them total a multiple of 7, **and** (2) both are leap or both are non-leap.

- Leap year: usually repeats after **28** years (2024 → 2052).
- Non-leap year: after 6 or 11 years depending on where the leap years fall. 2023 → **2034** (cumulative odd days 1, 3, 4, 5, 6, 8, 9, 10, 11, 13, 14 → 0 at 2034).
- Consecutive years never share a calendar.
- Century exceptions (1900, 2100) break the 28-year shortcut.

---

## 7. Exam traps

1. 1900 and 2100 are not leap years.
2. Crossing 29 February adds an extra odd day.
3. Same calendar needs the same leap status too.
4. Use complete years/months before the date, then add the date itself.

---

## 8. Practice questions (with solutions)

**Q1.** Which is a leap year?
(a) 1900 (b) 2000 (c) 2100 (d) 1800
**Answer: (b).**

**Q2.** Odd days in 400 years?
(a) 5 (b) 3 (c) 1 (d) 0
**Answer: (d).**

**Q3.** 1 January of a non-leap year is Monday. 1 October?
(a) Sunday (b) Monday (c) Tuesday (d) Wednesday
**Answer: (b).**

**Q4.** 1 February of a non-leap year is Thursday. 1 March?
(a) Wednesday (b) Thursday (c) Friday (d) Saturday
**Answer: (b).**

**Q5.** Can 2001 and 2002 have the same calendar?
(a) yes (b) no, 2001 adds 1 odd day (c) yes if both non-leap (d) can't say
**Answer: (b).**

**Q6.** 1 January 1901?
(a) Monday (b) Tuesday (c) Wednesday (d) Thursday
**Answer: (b).**

**Q7.** 26 January 1950?
(a) Wednesday (b) Thursday (c) Friday (d) Saturday
**Answer: (b).**

**Q8.** 1 January X to 1 January X + 5, with one leap year in between. Weekday shift?
(a) 5 (b) 6 (c) 0 (d) 4
**Answer: (b).**

**Q9.** 15 August 1947?
(a) Thursday (b) Friday (c) Saturday (d) Sunday
**Answer: (b).**

**Q10.** Odd days in 100 years?
(a) 5 (b) 4 (c) 2 (d) 1
**Answer: (a).**

**Q11.** Today is Monday. Day after 61 days?
(a) Friday (b) Saturday (c) Sunday (d) Thursday
**Answer: (b).**

**Q12.** Today is Wednesday. Day after 100 days?
(a) Thursday (b) Friday (c) Saturday (d) Sunday
**Answer: (b).**

**Q13.** 1 January 2023 was Sunday. 1 January 2025?
(a) Tuesday (b) Wednesday (c) Monday (d) Thursday
**Answer: (b).**

**Q14.** 8 February 2005 was Tuesday. 8 February 2004 was:
(a) Monday (b) Sunday (c) Saturday (d) Tuesday
**Answer: (b).**

**Q15.** The last day of a century can't be:
(a) Monday (b) Wednesday (c) Tuesday (d) Friday
**Answer: (c).**

**Q16.** In a non-leap year, 1 January is Monday. 31 December?
(a) Monday (b) Tuesday (c) Sunday (d) Wednesday
**Answer: (a).**

**Q17.** The calendar of 2023 will next repeat in:
(a) 2029 (b) 2034 (c) 2051 (d) 2028
**Answer: (b).**

**Q18.** The calendar of 2024 will next repeat in:
(a) 2030 (b) 2035 (c) 2052 (d) 2048
**Answer: (c).**

**Q19.** In a leap year, which month has the same calendar as January?
(a) October (b) April (c) March (d) November
**Answer: (b).** Also July.

**Q20.** In a non-leap year, which month has the same calendar as September?
(a) June (b) December (c) November (d) April
**Answer: (b).**

**Q21.** 1 January 2000 was Saturday. 1 January 2001?
(a) Sunday (b) Monday (c) Saturday (d) Tuesday
**Answer: (b).** 2000 is a leap year: +2.

**Q22.** Odd days in 300 years?
(a) 1 (b) 3 (c) 5 (d) 0
**Answer: (a).**
