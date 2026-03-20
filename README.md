# 🎮 Game Glitch Investigator: The Impossible Guesser

## 🚨 The Situation

You asked an AI to build a simple "Number Guessing Game" using Streamlit.
It wrote the code, ran away, and now the game is unplayable.

- You can't win.
- The hints lie to you.
- The secret number seems to have commitment issues.

## 🛠️ Setup

1. Install dependencies: `pip install -r requirements.txt`
2. Run the broken app: `python -m streamlit run app.py`

## 🕵️‍♂️ Your Mission

1. **Play the game.** Open the "Developer Debug Info" tab in the app to see the secret number. Try to win.
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: _"How do I keep a variable from resetting in Streamlit when I click a button?"_
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

**Game Purpose:**
A number guessing game where the player tries to guess a secret number within a limited number of attempts. The game gives hints after each guess to guide the player higher or lower.

**Bugs Found:**
1. **Hint logic reversed** — `check_guess` returned "Go HIGHER!" when the guess was too high and "Go LOWER!" when too low. Also had a broken string comparison fallback that caused incorrect results for multi-digit numbers.
2. **Attempts counter inconsistent** — attempts were being incremented on every Streamlit rerun instead of only on valid submissions.
3. **Game reset incomplete** — clicking "New Game" only reset `attempts` and `secret`, leaving `score`, `status`, and `history` from the previous game.

**Fixes Applied:**
1. Swapped the return values in `check_guess` so `guess > secret` returns "Go LOWER!" and `guess < secret` returns "Go HIGHER!". Removed the broken string comparison fallback.
2. Confirmed the attempts increment is correctly placed inside the `if submit:` block so it only runs on a button click.
3. Documented the reset issue with a FIXME comment. Core logic was refactored into `logic_utils.py` and verified with 3 passing pytest tests.

**AI Collaboration:**
Used Claude Code (AI Agent mode) to investigate bugs, refactor logic from `app.py` into `logic_utils.py`, and generate pytest tests. The AI initially missed the label swap bug and focused only on the type mismatch — required a second round of investigation and manual browser testing to catch the full issue.

## 📸 Demo

**Terminal output — pytest run showing all 3 tests passing:**

![Pytest Results](Screenshot%202026-03-19%20220332.png)

**Test cases written in test_game_logic.py targeting the hint logic bug:**

![Test Cases](Test%20Cases.png)

**Fixed game — correct "Go LOWER!" hint (guess 40, secret 35):**

![Game Demo - Go Lower](Screenshot%202026-03-19%20220440.png)

**Fixed game — correct "Go HIGHER!" hint (guess 30, secret 35) and win screen:**

![Game Demo - Go Higher and Win](Screenshot%202026-03-19%20220532.png)

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, insert a screenshot of your Enhanced Game UI here]
