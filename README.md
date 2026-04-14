# 🎵 Music Recommender Simulation

## Project Summary

This project simulates a content-based music recommendation engine. Given a catalog of 20 songs (each tagged with genre, mood, energy, danceability, valence, and acousticness) and a user "taste profile," the system scores every song against the user's preferences and returns a ranked list of top recommendations with human-readable explanations. It demonstrates how real platforms like Spotify turn raw audio attributes into personalized suggestions — and where those approaches can go wrong.

---

## How The System Works

Real-world recommenders typically combine two strategies: **collaborative filtering**, which finds users with similar listening histories and recommends what those peers enjoyed, and **content-based filtering**, which matches song attributes directly to a user's stated or inferred preferences. Platforms like Spotify blend both approaches — they analyze audio features (tempo, energy, danceability) alongside millions of user interactions (skips, saves, playlist adds) to build a rich model of taste.

This simulation focuses on the content-based side. Each song in the catalog has six numeric/categorical features: genre, mood, energy (0–1), tempo_bpm, valence (0–1), danceability (0–1), and acousticness (0–1). A `UserProfile` stores the listener's `favorite_genre`, `favorite_mood`, `target_energy`, and whether they `likes_acoustic` music.

### Algorithm Recipe

The scoring function evaluates every song in the catalog against the user profile:

- **Genre match** — if the song's genre equals the user's favorite genre → **+2.0 points**. This is the heaviest weight because genre is the strongest signal of broad taste.
- **Mood match** — if the song's mood equals the user's favorite mood → **+1.0 point**.
- **Energy similarity** — instead of rewarding high or low energy absolutely, the system calculates the gap between the song's energy and the user's target, then awards up to **+1.5 points** for closeness: `(1.0 − |song_energy − target_energy|) × 1.5`. A perfect energy match earns the full 1.5; a song at the opposite end of the scale earns nearly 0.
- **Danceability bonus** — `danceability × 0.3`, giving a small nudge (up to **+0.3**) toward more danceable tracks.
- **Valence bonus** — `valence × 0.2`, a small nudge (up to **+0.2**) toward more positive-sounding songs.
- **Acoustic preference** — if the user `likes_acoustic` and the song's acousticness ≥ 0.6 → **+0.5**. If the user does *not* prefer acoustic and the song's acousticness < 0.4 → **+0.3**.

After every song receives a score, the system sorts the full list from highest to lowest and returns the top *k* results along with a breakdown of why each song scored the way it did.

### Potential Biases in This Design

- Genre carries the most weight, so the system heavily favors songs in the user's stated favorite genre and may ignore excellent cross-genre matches.
- The dataset is small (20 songs) so some genres have only one or two representatives, making it easy for those genres to dominate or disappear from results.
- Danceability and valence bonuses are always positive, which slightly biases toward upbeat, danceable music even for users who prefer somber or still tracks.

---

## Getting Started

### Setup

1. Create a virtual environment (optional but recommended):

   ```bash
   python -m venv .venv
   source .venv/bin/activate      # Mac or Linux
   .venv\Scripts\activate         # Windows
   ```

2. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

3. Run the app:

   ```bash
   python -m src.main
   ```

### Running Tests

```bash
pytest tests/
```

You can add more tests in `tests/test_recommender.py`.

---

## Terminal Output

Running `python -m src.main` produces the following recommendations for all five profiles:

```
Loaded songs: 20
============================================================
  Profile: Happy Pop Listener
  Prefs:   {'genre': 'pop', 'mood': 'happy', 'energy': 0.8, 'likes_acoustic': False}
============================================================

  #1  Sunrise City by Neon Echo
       Genre: pop | Mood: happy | Energy: 0.82
       Score: 5.18
       Because: genre match: pop (+2.0); mood match: happy (+1.0); energy similarity (+1.47); danceability (+0.24); valence (+0.17); non-acoustic bonus (+0.3)

  #2  Gym Hero by Max Pulse
       Genre: pop | Mood: intense | Energy: 0.93
       Score: 4.01
       Because: genre match: pop (+2.0); energy similarity (+1.3); danceability (+0.26); valence (+0.15); non-acoustic bonus (+0.3)

  #3  Tropical Drift by Island Circuit
       Genre: reggaeton | Mood: happy | Energy: 0.8
       Score: 3.25
       Because: mood match: happy (+1.0); energy similarity (+1.5); danceability (+0.27); valence (+0.18); non-acoustic bonus (+0.3)

  #4  Rooftop Lights by Indigo Parade
       Genre: indie pop | Mood: happy | Energy: 0.76
       Score: 3.15
       Because: mood match: happy (+1.0); energy similarity (+1.44); danceability (+0.25); valence (+0.16); non-acoustic bonus (+0.3)

  #5  Concrete Jungle by MC Prism
       Genre: hip-hop | Mood: moody | Energy: 0.78
       Score: 2.13
       Because: energy similarity (+1.47); danceability (+0.25); valence (+0.11); non-acoustic bonus (+0.3)


============================================================
  Profile: Chill Lofi Student
  Prefs:   {'genre': 'lofi', 'mood': 'chill', 'energy': 0.35, 'likes_acoustic': True}
============================================================

  #1  Library Rain by Paper Lanterns
       Genre: lofi | Mood: chill | Energy: 0.35
       Score: 5.29
       Because: genre match: lofi (+2.0); mood match: chill (+1.0); energy similarity (+1.5); danceability (+0.17); valence (+0.12); acoustic preference bonus (+0.5)

  #2  Midnight Coding by LoRoom
       Genre: lofi | Mood: chill | Energy: 0.42
       Score: 5.20
       Because: genre match: lofi (+2.0); mood match: chill (+1.0); energy similarity (+1.4); danceability (+0.19); valence (+0.11); acoustic preference bonus (+0.5)

  #3  Focus Flow by LoRoom
       Genre: lofi | Mood: focused | Energy: 0.4
       Score: 4.22
       Because: genre match: lofi (+2.0); energy similarity (+1.42); danceability (+0.18); valence (+0.12); acoustic preference bonus (+0.5)

  #4  Spacewalk Thoughts by Orbit Bloom
       Genre: ambient | Mood: chill | Energy: 0.28
       Score: 3.15
       Because: mood match: chill (+1.0); energy similarity (+1.4); danceability (+0.12); valence (+0.13); acoustic preference bonus (+0.5)

  #5  Dreamstate by Orbit Bloom
       Genre: ambient | Mood: chill | Energy: 0.22
       Score: 3.02
       Because: mood match: chill (+1.0); energy similarity (+1.3); danceability (+0.1); valence (+0.12); acoustic preference bonus (+0.5)


============================================================
  Profile: High-Energy Rock Fan
  Prefs:   {'genre': 'rock', 'mood': 'intense', 'energy': 0.9, 'likes_acoustic': False}
============================================================

  #1  Thunder Road by Voltline
       Genre: rock | Mood: intense | Energy: 0.89
       Score: 5.09
       Because: genre match: rock (+2.0); mood match: intense (+1.0); energy similarity (+1.48); danceability (+0.21); valence (+0.1); non-acoustic bonus (+0.3)

  #2  Storm Runner by Voltline
       Genre: rock | Mood: intense | Energy: 0.91
       Score: 5.08
       Because: genre match: rock (+2.0); mood match: intense (+1.0); energy similarity (+1.48); danceability (+0.2); valence (+0.1); non-acoustic bonus (+0.3)

  #3  Gym Hero by Max Pulse
       Genre: pop | Mood: intense | Energy: 0.93
       Score: 3.17
       Because: mood match: intense (+1.0); energy similarity (+1.46); danceability (+0.26); valence (+0.15); non-acoustic bonus (+0.3)

  #4  Bass Cathedral by SubVolt
       Genre: edm | Mood: intense | Energy: 0.95
       Score: 3.13
       Because: mood match: intense (+1.0); energy similarity (+1.43); danceability (+0.28); valence (+0.12); non-acoustic bonus (+0.3)

  #5  Pixel Warfare by CyberHex
       Genre: electronic | Mood: intense | Energy: 0.88
       Score: 3.11
       Because: mood match: intense (+1.0); energy similarity (+1.47); danceability (+0.26); valence (+0.08); non-acoustic bonus (+0.3)


============================================================
  Profile: Edge Case: High-Energy + Sad
  Prefs:   {'genre': 'indie pop', 'mood': 'sad', 'energy': 0.9, 'likes_acoustic': False}
============================================================

  #1  Rooftop Lights by Indigo Parade
       Genre: indie pop | Mood: happy | Energy: 0.76
       Score: 4.00
       Because: genre match: indie pop (+2.0); energy similarity (+1.29); danceability (+0.25); valence (+0.16); non-acoustic bonus (+0.3)

  #2  Autumn Letters by Clara Veil
       Genre: indie pop | Mood: sad | Energy: 0.38
       Score: 3.94
       Because: genre match: indie pop (+2.0); mood match: sad (+1.0); energy similarity (+0.72); danceability (+0.14); valence (+0.08)

  #3  Gym Hero by Max Pulse
       Genre: pop | Mood: intense | Energy: 0.93
       Score: 2.17
       Because: energy similarity (+1.46); danceability (+0.26); valence (+0.15); non-acoustic bonus (+0.3)

  #4  Bass Cathedral by SubVolt
       Genre: edm | Mood: intense | Energy: 0.95
       Score: 2.13
       Because: energy similarity (+1.43); danceability (+0.28); valence (+0.12); non-acoustic bonus (+0.3)

  #5  Pixel Warfare by CyberHex
       Genre: electronic | Mood: intense | Energy: 0.88
       Score: 2.11
       Because: energy similarity (+1.47); danceability (+0.26); valence (+0.08); non-acoustic bonus (+0.3)


============================================================
  Profile: Adversarial: Metal Fan (no metal in catalog)
  Prefs:   {'genre': 'metal', 'mood': 'intense', 'energy': 0.95, 'likes_acoustic': False}
============================================================

  #1  Bass Cathedral by SubVolt
       Genre: edm | Mood: intense | Energy: 0.95
       Score: 3.20
       Because: mood match: intense (+1.0); energy similarity (+1.5); danceability (+0.28); valence (+0.12); non-acoustic bonus (+0.3)

  #2  Gym Hero by Max Pulse
       Genre: pop | Mood: intense | Energy: 0.93
       Score: 3.18
       Because: mood match: intense (+1.0); energy similarity (+1.47); danceability (+0.26); valence (+0.15); non-acoustic bonus (+0.3)

  #3  Storm Runner by Voltline
       Genre: rock | Mood: intense | Energy: 0.91
       Score: 3.04
       Because: mood match: intense (+1.0); energy similarity (+1.44); danceability (+0.2); valence (+0.1); non-acoustic bonus (+0.3)

  #4  Pixel Warfare by CyberHex
       Genre: electronic | Mood: intense | Energy: 0.88
       Score: 3.04
       Because: mood match: intense (+1.0); energy similarity (+1.4); danceability (+0.26); valence (+0.08); non-acoustic bonus (+0.3)

  #5  Thunder Road by Voltline
       Genre: rock | Mood: intense | Energy: 0.89
       Score: 3.02
       Because: mood match: intense (+1.0); energy similarity (+1.41); danceability (+0.21); valence (+0.1); non-acoustic bonus (+0.3)
```

---

## Experiments You Tried

### Experiment 1: Multiple User Profiles

Five distinct profiles were tested:

1. **Happy Pop Listener** (`genre=pop, mood=happy, energy=0.8`) — Top result: *Sunrise City* (5.18). Genre + mood + energy all aligned. Results feel correct; the top 5 are all upbeat tracks.
2. **Chill Lofi Student** (`genre=lofi, mood=chill, energy=0.35`) — Top result: *Library Rain* (5.29). Perfect energy match + genre + mood. The system correctly surfaces low-energy acoustic tracks.
3. **High-Energy Rock Fan** (`genre=rock, mood=intense, energy=0.9`) — Top results: *Thunder Road* and *Storm Runner* neck-and-neck (~5.09). Both are Voltline rock tracks, which makes sense.
4. **Edge Case: High-Energy + Sad** (`genre=indie pop, mood=sad, energy=0.9`) — *Rooftop Lights* (#1 at 4.00) beats *Autumn Letters* (#2 at 3.94) despite Autumn Letters matching mood. This is because Rooftop Lights' higher energy earns more energy-similarity points, and genre match alone carries +2.0. This reveals that energy can override mood in close calls.
5. **Adversarial: Metal Fan** (`genre=metal, mood=intense, energy=0.95`) — With no metal in the catalog, genre match is impossible. The system falls back to mood and energy, surfacing EDM and rock. Reasonable fallback behavior.

### Experiment 2: Comparing EDM vs Acoustic Profiles

The EDM profile (high energy, intense) recommended *Bass Cathedral* and *Gym Hero* — both high-energy, danceable. The acoustic profile (low energy, chill) shifted entirely toward *Library Rain* and *Spacewalk Thoughts*. The energy and acoustic features clearly differentiate these two listener types.

### Experiment 3 (Sensitivity): What If Genre Weight Is Halved?

Mentally tracing the math: if genre drops from +2.0 to +1.0, the Pop Listener's #1 (*Sunrise City*) would drop from 5.18 to ~4.18, while *Tropical Drift* (non-pop but happy, energy=0.8) would stay at 3.25. Genre match still wins, but the gap shrinks significantly. This means with a larger catalog, cross-genre surprises would become more common — a desirable outcome for discovery.

---

## Limitations and Risks

- **Tiny catalog**: 20 songs cannot represent real musical diversity. Some genres (folk, r&b, reggaeton) have a single track, so results for those tastes are predetermined.
- **No lyrical or language understanding**: The system knows nothing about lyrics, language, or cultural context.
- **Static user profile**: Real taste is dynamic — it changes with time of day, mood, and context. This system uses a single fixed profile.
- **Filter bubble risk**: The +2.0 genre weight means the system strongly reinforces the user's stated genre, making it unlikely to recommend outside it. Over time, this would narrow the user's exposure.
- **Positive-affect bias**: Danceability and valence bonuses are always additive, subtly penalizing introspective, slow, or melancholic music.

See [model_card.md](model_card.md) for deeper analysis.

---

## Reflection

Building this recommender made clear how even a simple scoring formula can produce results that "feel" personalized. The interplay of just a few weighted features — genre, mood, energy — is enough to create the illusion of a system that "knows" you. At the same time, the biases are right there in the weights: whichever feature gets the highest number dominates the output, and the dataset's composition silently shapes what's even possible to recommend.

The most interesting discovery was the edge case profile (high-energy + sad). The system had to choose between mood fidelity and energy fidelity, and energy won because of how the math shook out — a reminder that algorithmic "decisions" are really just arithmetic, and the values we assign to each feature encode our assumptions about what matters most. In a real product, those assumptions would affect millions of listeners without them ever seeing the formula.