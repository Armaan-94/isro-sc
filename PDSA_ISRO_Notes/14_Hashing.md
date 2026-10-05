# 14. Hashing

> **The dream:** find any item in **one step**, no matter how many items you store. Hashing gets close: compute an index directly from the key. The catch is **collisions** (two keys landing on the same slot). Every hashing question is either about computing slot positions with a given probing rule, or about load factor and expected probes.

---

## 1. Why hashing?

| Structure | Search (typical) |
|---|---|
| Unsorted array / linked list | O(n) |
| Sorted array (binary search) | O(log n) |
| BST | O(log n) average, O(n) worst |
| AVL / red-black tree | O(log n) worst |
| **Hash table** | **O(1) average** |

A **hash function** h maps a key from a huge universe (all possible roll numbers, all strings) to a slot in a table of size m: h: K → {0, 1, ..., m − 1}.

Hash tables do **not** keep keys in order: finding the minimum or doing range queries is slow.

---

## 2. Hash functions

### Properties of a good one

- Fast to compute.
- Spreads keys **uniformly** over the table.
- Few collisions; avoids clustering.
- Deterministic (same key, same slot).

### Common methods

**Division method:** h(k) = **k mod m**.
- Choose **m prime**, not close to a power of 2. If m = 2^p, h(k) just takes the last p bits of k, so keys with similar low bits collide systematically.

**Multiplication method:** h(k) = ⌊m × (k·A mod 1)⌋ with 0 < A < 1 (Knuth suggests A ≈ 0.618). Here m can be a power of 2.

**Mid-square method:** square the key, take the middle digits.
Example: k = 3205, k² = 10272025, take the middle two digits → **72**.

**Folding method:** split the key into parts and add them.
Example: k = 123456789, parts 123 + 456 + 789 = 1368; for a 1000-slot table → **368**.

**Universal hashing:** pick the hash function randomly from a family at runtime, so no fixed input pattern is always bad.

---

## 3. Collisions are inevitable

If there are more possible keys than slots (always), some keys must share slots (pigeonhole principle). Even with fewer keys than slots, collisions come early: the **birthday paradox** says with 23 people and 365 days, the chance of a shared birthday exceeds 50%.

Probability that n keys land in **distinct** slots of m (uniform hashing): (m/m) × ((m − 1)/m) × ... × ((m − n + 1)/m).
Example: m = 10, n = 3: 1 × 0.9 × 0.8 = **0.72**, so a 28% chance of at least one collision.

Expected number of colliding pairs: **n(n − 1)/(2m)**.

**Load factor:** **α = n / m** (keys per slot). Performance depends on α, not on n alone.

---

## 4. Collision resolution 1: Separate chaining (open hashing)

Each slot holds a **linked list** of all keys hashing there.

- Insert: O(1) (add to the front of the list).
- Search/delete: O(1 + α) average.
- α can be **greater than 1**. The table never "fills up".
- Deletion is easy.
- Extra memory for pointers; poorer cache behaviour.

Expected probes (simple uniform hashing):
- Unsuccessful search: **α** (or 1 + α counting the slot access).
- Successful search: **1 + α/2**.

### Worked example (GATE 2014)

m = 9, h(k) = k mod 9, keys 5, 28, 19, 15, 20, 33, 12, 17, 10.

| Key | k mod 9 |
|---|---|
| 5 | 5 |
| 28 | 1 |
| 19 | 1 |
| 15 | 6 |
| 20 | 2 |
| 33 | 6 |
| 12 | 3 |
| 17 | 8 |
| 10 | 1 |

Slots: 0: –, **1: 28 → 19 → 10**, 2: 20, 3: 12, 4: –, 5: 5, 6: 15 → 33, 7: –, 8: 17.
Max chain **3**, min **0**, average 9/9 = **1**.

---

## 5. Collision resolution 2: Open addressing (closed hashing)

All keys live **inside the table**. On a collision, **probe** other slots in a fixed sequence h(k, 0), h(k, 1), h(k, 2), ... until an empty slot is found.

- Needs **α < 1** (table can fill up). Performance drops sharply as α → 1.
- Better cache behaviour; no pointers.

> **Terminology trap:** **open addressing = closed hashing** (everything inside the table). **Chaining = open hashing** (keys stored "outside" in lists). The names are confusingly opposite.

### 5.1 Linear probing

```
h(k, i) = (h'(k) + i) mod m,   i = 0, 1, 2, ...
```

Try the home slot, then the next, then the next...

**Problem: primary clustering.** Occupied slots form long runs; any key hashing anywhere into a run extends it. The chance a new key lands right after a run grows with the run's length.

**Worked example (GATE 2010).** m = 10, h(k) = k mod 10, insert 12, 18, 13, 2, 3, 23, 5, 15.

| Key | Home | Probes | Final slot |
|---|---|---|---|
| 12 | 2 | 2 free | **2** |
| 18 | 8 | 8 free | **8** |
| 13 | 3 | 3 free | **3** |
| 2 | 2 | 2 ✗, 3 ✗, 4 | **4** |
| 3 | 3 | 3 ✗, 4 ✗, 5 | **5** |
| 23 | 3 | 3, 4, 5 ✗, 6 | **6** |
| 5 | 5 | 5, 6 ✗, 7 | **7** |
| 15 | 5 | 5, 6, 7, 8 ✗, 9 | **9** |

Final table: [–, –, 12, 13, 2, 3, 23, 5, 18, 15].

### 5.2 Quadratic probing

```
h(k, i) = (h'(k) + c₁·i + c₂·i²) mod m     (often simply + i²)
```

Jumps grow: +1, +4, +9, ... This breaks up primary clusters.

**Problem: secondary clustering.** Keys with the **same home slot** follow the **same** probe sequence. Also, it may fail to find an empty slot even when one exists, unless m is prime and α ≤ 1/2.

**Worked example.** m = 7, h(k) = k mod 7, probe (h + i²) mod 7. Insert 76, 93, 40, 47, 10, 55.

| Key | Home | Probes | Final |
|---|---|---|---|
| 76 | 6 | 6 | **6** |
| 93 | 2 | 2 | **2** |
| 40 | 5 | 5 | **5** |
| 47 | 5 | 5 ✗, 5+1 = 6 ✗, 5+4 = 9 → 2 ✗, 5+9 = 14 → 0 | **0** |
| 10 | 3 | 3 | **3** |
| 55 | 6 | 6 ✗, 7 → 0 ✗, 10 → 3 ✗, 15 → 1 | **1** |

Final: [47, 55, 93, 10, –, 40, 76].

### 5.3 Double hashing

```
h(k, i) = (h₁(k) + i · h₂(k)) mod m
```

The **step size depends on the key** (via a second hash h₂), so keys that collide at home usually follow different sequences. **Best distribution** of the three; close to ideal uniform hashing.

Requirements: h₂(k) ≠ 0, and h₂(k) should be relatively prime to m (choose m prime). A common choice: h₂(k) = R − (k mod R) for a prime R < m.

**Worked example.** m = 13, h₁(k) = k mod 13, h₂(k) = 7 − (k mod 7). Insert 18, 41, 22, 44, 59, 32, 31, 73.

| Key | h₁ | h₂ | Probes | Final |
|---|---|---|---|---|
| 18 | 5 | | 5 | **5** |
| 41 | 2 | | 2 | **2** |
| 22 | 9 | | 9 | **9** |
| 44 | 5 | 7 − 2 = 5 | 5 ✗, 10 | **10** |
| 59 | 7 | | 7 | **7** |
| 32 | 6 | | 6 | **6** |
| 31 | 5 | 7 − 3 = 4 | 5 ✗, 9 ✗, 13 → 0 | **0** |
| 73 | 8 | | 8 | **8** |

### 5.4 Deletion in open addressing

You **can't** just empty a slot: a later search for a key that was probed past this slot would stop early and wrongly report "not found". Instead, mark it **DELETED** (a tombstone): searches continue past it, inserts may reuse it.

### 5.5 Expected probes (uniform hashing, α < 1)

| | Unsuccessful search / insert | Successful search |
|---|---|---|
| Open addressing (ideal/double hashing) | **1 / (1 − α)** | (1/α) ln(1/(1 − α)) |
| Linear probing | ½ (1 + 1/(1 − α)²) | ½ (1 + 1/(1 − α)) |

At α = 0.5: ideal unsuccessful = 2, linear-probing unsuccessful = 2.5. At α = 0.9: ideal = 10, linear = 50.5. That's why open-addressing tables are resized (rehashed) when α passes about 0.7.

---

## 6. Chaining vs open addressing

| | Chaining | Open addressing |
|---|---|---|
| Where keys live | Linked lists outside the table | Inside the table |
| Load factor | Can exceed 1 | Must be < 1 |
| Fills up? | Never | Yes |
| Deletion | Easy | Needs tombstones |
| Cache | Worse | **Better** |
| Extra memory | Pointers | None (but some empty slots needed) |
| Sensitivity to α and clustering | Lower | Higher |
| Good when | Number of keys unknown | Number of keys known, memory tight |

### Rehashing

When α gets too high, allocate a bigger table (often about double, prime size) and re-insert every key using the new hash function. O(n) occasionally; amortised O(1) per insertion.

### Perfect hashing

For a **static** set of keys, a two-level scheme gives **O(1) worst-case** lookup.

---

## 7. Exam traps

1. Hashing: O(1) **average**, O(n) worst case (all keys in one slot).
2. Choose m prime for the division method.
3. α = n/m; chaining allows α > 1, open addressing needs α < 1.
4. Open addressing = **closed** hashing; chaining = **open** hashing.
5. Linear probing → **primary** clustering. Quadratic → **secondary** clustering. Double hashing → best.
6. Deleting in open addressing needs a tombstone.
7. Expected probes for an unsuccessful search with open addressing: 1/(1 − α).
8. Trace probe sequences carefully, wrapping around mod m.

---

## 8. Practice questions

**Q1.** m = 9, h(k) = k mod 9, chaining. Keys 5, 28, 19, 15, 20, 33, 12, 17, 10. Max, min and average chain lengths?
(a) 3, 0, 1 (b) 3, 3, 3 (c) 4, 0, 1 (d) 3, 0, 2

**Answer: (a).**

---

**Q2.** m = 10, h(k) = k mod 10, linear probing. Insert 12, 18, 13, 2, 3, 23, 5, 15. Where does 15 end up?
(a) 5 (b) 7 (c) 9 (d) 0

**Answer: (c).**

---

**Q3.** m = 11, h(k) = k mod 11, linear probing. Insert 22, 33, 44 in order. Slot of 44?
(a) 0 (b) 1 (c) 2 (d) 11

**Answer: (c).** All hash to 0: 22 → 0, 33 → 1, 44 → 2.

---

**Q4.** Same as Q3 but quadratic probing (h + i²). Slot of 44?
(a) 2 (b) 4 (c) 1 (d) 9

**Answer: (b).** 44: 0 ✗, 0 + 1 = 1 ✗ (33 is there), 0 + 4 = 4 ✓.

---

**Q5.** Primary clustering is a weakness of:
(a) chaining (b) linear probing (c) double hashing (d) perfect hashing

**Answer: (b).**

---

**Q6.** An open-addressing table has load factor 0.75. Expected probes for an unsuccessful search (uniform hashing)?
(a) 1.33 (b) 2 (c) 4 (d) 0.75

**Answer: (c).** 1/(1 − 0.75).

---

**Q7.** In chaining with n = 200 keys and m = 50 slots, the load factor is:
(a) 0.25 (b) 4 (c) 50 (d) 200

**Answer: (b).**

---

**Q8.** Which statement is TRUE?
(a) Open addressing is also called open hashing (b) Chaining stores all keys within the table (c) In open addressing α must be less than 1 (d) Double hashing suffers more from clustering than linear probing

**Answer: (c).**

---

**Q9.** Why not simply empty a slot when deleting in linear probing?
(a) It wastes memory (b) Later searches might stop early at that empty slot and miss keys placed beyond it (c) Deletion isn't allowed (d) The hash function would change

**Answer: (b).**

---

**Q10.** Using mid-square hashing, key 44, table of 100 slots (take middle two digits of k² padded to 4 digits). Slot?
(a) 93 (b) 36 (c) 19 (d) 44

**Answer: (a).** 44² = 1936 → middle two digits **93**.

---

**Q11.** With uniform hashing, 3 keys into a table of 5 slots. Probability of no collision?
(a) 0.48 (b) 0.6 (c) 0.52 (d) 0.24

**Answer: (a).** 1 × 4/5 × 3/5 = 12/25 = 0.48.

---

**Q12.** Worst-case search time in a hash table with chaining is:
(a) O(1) (b) O(log n) (c) O(n) (d) O(n log n)

**Answer: (c).** All keys may land in one chain.

---

**Practice questions:** [2.14 Hashing](../ISRO_CS_Question_Bank/02_Programming_DS_Algorithms/2.14_Hashing.md)
