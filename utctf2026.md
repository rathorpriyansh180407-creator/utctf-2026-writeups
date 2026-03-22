<!-- ## challange-1 -->
# Breadcrumbs

**Author:** Priyansh Rathor
**Category:** Misc
**Difficulty:** Easy–Medium
**Challenge Author:** Garv (@GarvK07)

---

## Problem Statement

We were given a GitHub link. Told to follow the clues to get to the final answer. Each step had a puzzle that used techniques to hide the next clue.

---

## Initial Analysis

When we opened the link we found a text file with a message and a strange encoded string at the bottom. This made us think it was some kind of code like Base64.

The hint "follow the breadcrumbs" made us think that each step would lead to the one.

---

## Roadblocks

* We found a Python script that seemed to reverse a string. It was not really important.
* The real clue was hidden in the comments, which was a trick to distract us.

This taught us a lesson: 
> Not all code is important. Sometimes it is just there to confuse us.

---

## Step-by-Step Solution

### Step 1: Base64 Decoding

The first link had a string:

```
aHR0cHM6Ly9naXN0LmdpdGh1Yi5jb20vZ2FydmswNy9iYTQwNjQ2MGYyZTkzMmI1NDk2Y2EyNTk3N2JlMjViZQ==
```

This looked like Base64 code.

Decoding:
Go to a decoder such as:
https://gchq.github.io/CyberChef/

or

```bash
echo "aHR0cHM6Ly9naXN0LmdpdGh1Yi5jb20vZ2FydmswNy9iYTQwNjQ2MGYyZTkzMmI1NDk2Y2EyNTk3N2JlMjViZQ==" | base64 -d
```

Output:

```
https://gist.github.com/garvk07/ba406460f2e932b5496ca25977be25be
```

---

### Step 2: Finding the Next Clue

The next link had a poem with a hidden link at the bottom:

```
p.s. https://gist.github.com/garvk07/963e70be662ea81e96e4e63553038d1a
```
We just had to read it

---

### Step 3: Hexadecimal Decoding

The next link had a Python file, with a hex string:

```
68747470733a2f2f676973742e6769746875622e636f6d2f676172766b30372f3564356566383539663533306333643539336134613363373538306432663239
```

We converted it from hex to ASCII:

```bash
echo "68747470733a2f2f676973742e6769746875622e636f6d2f676172766b30372f3564356566383539663533306333643539336134613363373538306432663239" | xxd -r -p
```

Output:

```
https://gist.github.com/garvk07/5d5ef859f530c3d593a4a3c7580d2f29
```

---

### Step 4: ROT13 Decoding

The final link had a strange string::

```
hgsynt{s0yy0j1at_gu3_pe4jy_ge41y}
```

It looked like a ROT13 code.

We decoded it:
```
hgsynt{s0yy0j1at_gu3_pe4jy_ge41y}
→ utflag{f0ll0w1ng_th3_cr4wl_tr41l}
```

---

## Final Flag

```
utflag{f0ll0w1ng_th3_cr4wl_tr41l}
```

---

## Tools Used

* `base64` (CLI)
* `xxd` (hex decoding)
* CyberChef (optional)
* Browser

---

## Key Takeaways

* CTF challenges often use **layered encodings** (Base64 → Hex → Cipher).
* Always look at **comments and hidden sections** Not the visible code.
* Be aware of **intentional misdirection**.
* Recognizing patterns can help you solve the challenge faster.

---

## What I Learned

This challenge helped me:

* Identify codes quickly.
* Test decoding methods systematically.
* Not make things too complicated. Sometimes the answer is simple but hidden.

It also improved my ability to distinguish between **useful information and distractions**, which is crucial in CTF problem-solving.


<!-- ## Challenge-2 -->
# Hidden in Plain Sight

**Author:** Priyansh Rathor
**Category:** Misc 
**Difficulty:** Medium
**Challenge Author:** Elie (@Razboy20)

---

## Problem Statement

We are given a text string:

> "Hidden in Plain Sight"

The challenge hint says that a hidden message is embedded in it even though it looks completely normal.
---

## Initial Analysis

At first glance, the string looks ordinary. However, the title *“Hidden in Plain Sight”* strongly suggests that the data is concealed in a way that is not immediately visible.

I thought about what could be hidden in the string. I considered:

* invisible characters
* zero-width Unicode
* hidden encodings within the text

---

## Roadblocks

When inspecting the string normally:

* No unusual characters were visible
* Copy-paste and visual inspection revealed nothing

This indicated that the hidden data was likely encoded using **non-printable or invisible Unicode characters**.

---

## Deep Dive: Unicode Tag Characters

When I looked at the string using a hex viewer or Unicode inspector I found a sequence of characters in the range U+E0000 to U+E007F. These characters are part of the **Unicode Tag Characters block**. 

They are:

* invisible (zero-width)
* not rendered in standard text
* suitable for hiding data

These characters map directly to ASCII:

```
U+E0061 → 'a'
```

So, subtracting `0xE0000` from each codepoint reveals the original message.

---

## Extraction(Using CyberChef)

Instead of scripting, tools like **CyberChef** can also be used:

* Paste the raw string
* Apply:

  * *Escape Unicode Characters*
  * or *To Hexdump*

This reveals the hidden sequence directly.

---

##  Final Flag

```
utflag{1nv1s1bl3_un1c0d3}
```

---

## Tools Used

* CyberChef
* Unicode / hex inspection tools

---

##  Key Takeaways

* Unicode can be used to hide data using **invisible characters**
* I should always inspect text beyond what is visually rendered.
* Tag characters are a practical steganography technique in CTFs

---

## 💡 What I Learned

This challenge introduced me to **Unicode-based steganography**, which was new to me.

I learned:

* how invisible Unicode characters can encode information
* how to detect hidden data in normal-looking text

<!-- ## Challenge-3 -->
# Jail Break

**Author:** Priyansh Rathor
**Category:** Misc
**Difficulty:** Medium
**Challenge Author:** Garv (@GarvK07)

---

## Problem Statement

We have a Python sandbox. We need to escape it to get a hidden flag. The sandbox executes what we type using a exec() function in a limited environment.
---

## Initial Analysis

From inspecting the code, I observed:

* What we type is executed using exec() in a loop
* Standard Python built-ins are replaced with a restricted set
* Some words like import os eval system and secret are not allowed

There was also a key hint in the source code:

> `_secret is in globals but not documented`

This suggested that a hidden function `_secret()` exists which likely returns the flag.

---

## Roadblocks

* Directly calling `_secret()` was not possible because the word `"secret"` is blocked
* No access to `import`, `os`, or other typical escape methods
* The environment appeared heavily restricted

I had to find a way to get around the filter that blocked certain words.

---

## Key Insight

Even though some words were blocked, some tools, like vars() still worked.

In Python:

* `vars()` returns a dictionary of the current scope
* This includes accessible variables and functions

* This means `_secret` could still be accessed indirectly.

---

## Exploitation

### Step 1: Get Around the Filter

Instead of typing `"secret"` directly, construct it dynamically:

```python
fn_name = "_sec" + "ret"
```

This avoids detection by the filter.

---
### Step 2: Access the Hidden Function

I used vars() to get the function from the scope:

```python
fn = vars()[fn_name]
```

---

### Step 3: Execute the Function

```python
print(fn())
```

---

## Final Payload

```python
fn_name = "_sec" + "ret"
fn = vars()[fn_name]
print(fn())
```

---

## Final Flag

```
id="6z9x4p"
utflag{py_ja1l_3sc4p3_m4st3r}
```

---

##  Tools Used

* Python interpreter
* Manual code analysis

---

## Key Takeaways

* Blacklist-based filters are **not secure**
* Python allows dynamic access to variables using `vars()` / `globals()`
* Building words dynamically can get around filters
* Secure sandboxes need to limit what you can do not what words you can use

---

## What I Learned

This challenge introduced me to **Python sandbox escape techniques**.

I learned:

* How limited environments can still have tools
* How to get around word filters using dynamic word building
* How vars() and globals() can be used to find and execute functions

This improved my understanding of why **secure sandboxing is difficult to implement correctly**.