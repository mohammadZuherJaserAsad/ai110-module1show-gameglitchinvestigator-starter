# Phase 2 Reflection – Game Glitch Investigator

---

## Bugs Identified

1. **Bug: Hint does not match the guess**
   - Expected: The hint should correctly tell me if my guess is too high or too low
   - Actual: The hint was reversed — guessing too low showed "Go LOWER!" and guessing too high showed "Go HIGHER!"

2. **Bug: Attempts counter is inconsistent**
   - Expected: The attempts counter should decrease by 1 after each valid guess
   - Actual: The counter was being incremented incorrectly, sometimes on every rerun instead of once per submission

3. **Bug: Game reset issue**
   - Expected: Clicking "New Game" should fully reset score, attempts, history, and status
   - Actual: Only attempts and the secret number were reset; score, status, and history persisted from the previous game

---

## How I Fixed the Bugs

### Bug 1 – Hint Logic (check_guess)
The original code had the labels swapped:
- `guess > secret` was returning "Go HIGHER!" (wrong — you need to go lower)
- `guess < secret` was returning "Go LOWER!" (wrong — you need to go higher)

I also removed a broken fallback that used string comparison (`g > secret`), which gave wrong results for multi-digit numbers due to Python's lexicographic string ordering.

**Fix:** Swapped the return values so `guess > secret` returns "Go LOWER!" and the else branch returns "Go HIGHER!"

### Bug 2 – Attempts Counter
The attempts increment was placed correctly inside the `if submit:` block, so it only runs on a button click. I added a comment to clarify this so future readers understand it is intentional.

### Bug 3 – Game Reset
The `New Game` button only reset `attempts` and `secret`. Score, status, and history carried over from the previous session. I fixed this by adding a full reset inside the `if new_game:` block, and added a FIXME + FIX comment pair to document what was changed and why.

---

## 2. How did you use AI as a teammate?

I used Claude Code (AI assistant) throughout Phase 2 to help investigate and fix the bugs.

**What the AI got right:**
- Correctly identified that the hint labels in `check_guess` were swapped
- Suggested removing the broken string comparison fallback that used lexicographic ordering
- Helped move all four logic functions (`check_guess`, `parse_guess`, `get_range_for_difficulty`, `update_score`) from `app.py` into `logic_utils.py` cleanly
- Added `FIXME` + `FIX` comment pairs near each bug location
- Fixed the pytest tests to unpack the tuple returned by `check_guess`

**What the AI got wrong (or needed correction):**
- In an early fix attempt, the AI only removed the even/odd branching that converted the secret to a string, but did not catch that the Higher/Lower labels were also swapped. The hint was still wrong after the first fix — I had to retest in the browser and ask again
- The AI initially suggested the issue was only the type mismatch (int vs string), missing the deeper label reversal bug. This required a second round of investigation

---

## 3. Debugging and testing your fixes

### Manual testing in the browser (Streamlit)
- Ran `streamlit run app.py` and played several rounds
- Guessed `-1000` when the secret was `94` — confirmed the hint now correctly shows "Go HIGHER!"
- Guessed a number above the secret — confirmed the hint shows "Go LOWER!"
- Clicked "New Game" and verified a new secret was generated

### Automated testing with pytest
Ran the following command:
```
python -m pytest tests/test_game_logic.py -v
```

Results:
```
tests/test_game_logic.py::test_winning_guess PASSED
tests/test_game_logic.py::test_guess_too_high PASSED
tests/test_game_logic.py::test_guess_too_low PASSED

3 passed in 0.02s
```

All three tests pass after:
1. Moving `check_guess` into `logic_utils.py`
2. Updating the test assertions to unpack the returned tuple (`outcome, message = check_guess(...)`)
