# Tutorial: CyberChef — decoding & classical crypto

**What it is:** the GCHQ's browser-based "Cyber Swiss Army Knife" — no install, works offline at https://gchq.github.io/CyberChef. The exam home for every "decode this string" question.

Quick-start workflow:
1. Open CyberChef, paste the mystery string in the **Input** box.
2. Drag **Magic** into the Recipe.
3. If Magic finds something readable → done. If not → try the manual operations below.
4. Read the **Output** box; chain operations in the Recipe as needed (each output feeds the next).

## The operations that solve 90% of exam questions
| You see | Operation to use |
|---------|------------------|
| `=` padding, `A-Za-z0-9+/` | **From Base64** |
| only `0-9a-f` chars | **From Hex** |
| letters shifted / garbled Latin | **ROT13** (or **Rotate Brute Force** for unknown shift) |
| `%20`, `%3D` in strings | **URL Decode** |
| `A1Z26`-style numbers | **From Decimal** |
| reversed-ish alphabet text | **Atbash Cipher** |
| unknown key XOR | **XOR Brute Force** |
| dots and dashes | **From Morse Code** |
| `0`/`1` blobs | **From Binary** |

## Magic — the one operation to remember
- **Magic** auto-detects the encoding/crypto and rebuilds the recipe for you.
- Finds multiple layers: Magic happily decodes Base64 → ROT13 → Base64 in one shot.
- **Magic options:** raise **Depth** (default 3) if you suspect more layers; enable **Extensive language support**; it scores outputs by whether they look like real text.
- Magic shows the recipe it used — click through to load it and keep working from there.

## Chaining recipes (recipes = assembly line)
Drag operations into the Recipe pane in order; each one transforms what came before:
```
Input: SGVsbG8gV29ybGQh
From Base64 → From Hex → ROT13   (whatever the chain needs)
```
Use **Bake** to process (auto-bake usually on), and the 🔥 icon / "Save recipe" to bookmark a chain.

## Exam-style examples
```
NTUgNGIgMjM=                → From Base64 → "55 4b 23" (hex string)
                            → From Hex → "UK#"
4a 65 61 6e                 → From Hex → "Jean"
%68%75%6e%74%65%72%32       → URL Decode → "hunter2"
Guvf vf n frperg            → ROT13 → "This is a secret"
```

## Gotchas
- **Magic first, manual second** — but check Magic's confidence; garbage output means the wrong path (e.g., From Hex on non-hex data yields nothing).
- Base64 vs hex confusion: hex only ever contains `0-9a-f`; Base64 has the full alphabet and often `=` padding.
- Encoding ≠ encryption: Base64/hex/ROT13 are trivially reversible (no key) — if a key is mentioned, think XOR, and use **XOR Brute Force**.
- Works **offline** once loaded — if the exam VM has no internet, CyberChef still runs from the browser (or clone the page ahead of time).

**Deep dive (classical crypto, frequency analysis):** [02-cryptography-password-cracking.md](../02-cryptography-password-cracking.md) · lecture theory: [09-lecture-concepts.md](../09-lecture-concepts.md) (W1)

← [Back to index](../README.md)
