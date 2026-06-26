# Ayah -- Quranic Vocabulary Learning App
## Complete Reference for Development

Last updated: 2026-06-23

> UPDATE LOG (2026-06-23): Two new feature areas were added as separate prototype
> files so they can be tried and discarded without touching the main app. The new
> documentation lives in two clearly marked sections below:
>   1. "## Revision Tab (NEW - 2026-06-23)"  -> describes ayah-revision.html
>   2. "## Lesson Enhancements (NEW - 2026-06-23)"  -> describes ayah-lessons-enhanced.html
> The "File Structure" section was also updated to list both new files. Search this
> file for the tag "NEW - 2026-06-23" to jump to every added or changed block.

---

## What This App Is

A mobile-first Quranic vocabulary learning app. Goal: help users who can read Arabic but do not understand it learn the meaning of the Quran's words through a spaced-repetition flashcard loop.

The app teaches the most frequently occurring words in the entire Quran first, ranked by frequency. A small set of high-frequency words covers a huge share of the text. Teaching them first means a learner sees their comprehension jump fast and early, which keeps them motivated.

**Target user:** Someone who can read Arabic script but does not understand the meaning. Muslim, motivated to understand the Quran directly.

---

## Core Concept: Frequency-First Learning

Instead of going surah by surah, the app teaches the most frequently occurring words in the entire Quran first. Each word is reinforced by showing the actual verses (ayahs) where it appears, so the meaning clicks in real context.

### Verified Numbers

- Total words in the Quran: ~77,430 (Quranic Arabic Corpus)
- Unique word forms: ~14,870
- Roots: ~1,600 | Lemmas: ~3,357
- The top ~500 most frequent words cover approximately 75% to 85% of all word occurrences in the Quran. The app promises 75% comprehension -- a safe, conservative target.

### Word Unit: Hybrid Model (LOCKED)

- Rank by most frequent **surface form** (exact spelling as it appears in verses)
- On every card, also show the **root** and its word-family so patterns click
- Example: `ٱلرَّحْمَـٰنِ` and `ٱلرَّحِيمِ` are separate entries in the ranked list, but each card surfaces the shared root `ر ح م` and its relatives
- "Most frequent first" is always honored

---

## Scope

### Phase 1 (Build Now)

- The ~500 most frequent unique word forms, taught in frequency order
- Covers ~75% of the Quran's word occurrences
- 15 milestone bands (5% comprehension each), each made of short lessons
- Each word reinforced with the real ayahs it appears in
- Arabic pronunciation audio for every word

### Phase 2 (Deferred -- Do Not Build Yet)

- The remaining ~14,000+ less-frequent unique word forms (the long tail)
- These cover the final ~25% of the Quran
- Will be planned after Phase 1 ships with real usage data
- Likely a different mode (in-context only, surah-by-surah, or "fill the gaps") rather than the full flashcard loop

---

## Lesson Structure

- The journey is mapped as a progress path from 0% to 75% comprehension
- Each milestone = 5% of the Quran. Reaching 75% takes 15 milestones
- Math: 500 words = ~75%, so each 1% is ~6.7 words, each 5% milestone is ~33 words
- 33 words is too many for one sitting, so each 5% milestone is broken into several short lessons
- Target per lesson: ~7 to 10 words, ~5 minutes each
- Finishing all the short lessons in a band fills that 5% and unlocks a celebration and the next band

### Home / Progress Screen Shows

- Current comprehension % (0% to 75%)
- Words mastered count
- The next milestone target
- How many lessons remain to hit the next 5% band

---

## Data Requirements

### The 500-Word Frequency Dataset

Build an ordered list for all 500 words with these fields per entry:

```json
{
  "rank": 1,
  "arabic": "ٱللَّهِ",
  "transliteration": "Allah",
  "english_meaning": "God / Allah",
  "root": "ا ل ه",
  "root_family": ["إِلَـٰهٌ", "أُولَـٰئِكَ"],
  "quran_frequency": 2699,
  "representative_ayah": {
    "surah": 1,
    "verse": 1,
    "word_position": 2,
    "audio_url": "https://audio.qurancdn.com/wbw/001_001_002.mp3"
  }
}
```

**Source:** Quranic Arabic Corpus + Quran Foundation API

**Audio URL pattern:** `https://audio.qurancdn.com/wbw/{surah_padded}_{verse_padded}_{word_padded}.mp3`

Example: surah 1, verse 1, word 1 = `https://audio.qurancdn.com/wbw/001_001_001.mp3`

---

## Audio

**Decision (LOCKED):** Quran.com word-by-word mp3s (real human recitation, per-word, keyed by `surah_verse_word`)

- Audio hosted on CDN at `https://audio.qurancdn.com/`
- Each of the 500 words maps to one representative ayah occurrence; play that word's clip
- Backup option: Buraaq quran-audio-text-dataset (Hugging Face, ~77,429 individual word mp3s) for self-hosting if CDN is unavailable
- Respects the existing audio on/off setting; tap any Arabic word to hear it

**Licensing to-do before shipping:** Confirm terms for Quran.com/QuranWBW word-by-word audio. Tanzil text and Pickthall translation are already cleared (public domain/CC). Buraaq dataset and quran-align are CC.

---

## Learning Loop (Per Word)

Each word goes through this sequence:

1. **Introduce** -- Show the Arabic word, transliteration, English meaning, root, and a real verse where it appears (word highlighted). Play audio automatically if audio is on.
2. **Recall (Flip Card)** -- Show Arabic only, user taps to flip and reveal meaning.
3. **Pick the Arabic** -- Show English meaning, user picks the correct Arabic from 4 options.
4. **Pick the English** -- Show Arabic, user picks the correct English from 4 options.
5. **Fill in the blank** -- Show a real verse with the target word blanked out; user completes it.

Wrong answers re-queue 2 to 4 cards later in the same session.

---

## Ayah Context Step (New Feature)

After a word is learned, show 1 to 3 real verses where it appears. The target word is highlighted in the verse. This is the "it clicks in real context" moment.

Fields needed per ayah display:
- Full Arabic verse text
- English translation (Pickthall or Clear Quran)
- Surah name and number, verse number
- Target word highlighted (by word position index)

---

## Engagement Features (Build These -- LOCKED)

### Daily Goal + Streak Reminders
- User sets a daily word goal (e.g., 5 words/day)
- Push notification or in-app nudge to keep the streak alive
- Streak counter displayed prominently on home screen

### XP, Levels, and Badges
- Earn XP points for each word learned and each lesson completed
- Unlock a badge at each 5% milestone (15 badges total)
- Badge displayed on profile/progress screen

### Story / Context Mode
- After learning a word, reveal a real or famous verse the user can now understand
- This is the payoff moment: "You can understand this verse now"
- Show the full verse, translation, and which words in it the user already knows (highlighted)

### Not Prioritized Now (revisit later)
- Hearts/lives system
- Friends leaderboard
- Shareable progress cards

---

## Progress and Persistence

Track per word:
- `mastered: boolean`
- `confidence_level: 1 to 5` (spaced repetition factor)
- `next_review_date: ISO date`
- `times_seen: number`
- `times_correct: number`

Track overall:
- `words_mastered: number`
- `comprehension_pct: float` (words_mastered / 500 * 75)
- `current_milestone: 1 to 15`
- `streak_days: number`
- `last_session_date: ISO date`
- `xp_total: number`
- `badges_earned: array`

**Storage:** localStorage for single-file HTML/JS prototype. Design for easy migration to a real backend later.

---

## Design System (Keep Exactly As Built)

### Colors
- Background: dark navy radial gradient (`#03060f` to `#1d3068`)
- Gold (primary accent): `#c9a96e` / `#e8c98a` / `#fbeeb8`
- Green (meanings): `#78d9a0`
- No gradients on text; subtle glows only

### Typography
- Arabic text: Scheherazade New
- UI headings: Cormorant Garamond
- UI body/labels: Poppins

### Motifs and Animations
- Self-drawing gold arch (lesson complete celebration)
- Girih star tiles (decorative panels)
- Floating Arabic glyphs (background texture)
- Particle dust effects
- Letter burst on per-word celebration
- Wheat sheaves on lesson complete
- All existing animations must be preserved and extended to new screens

### Celebration Overlays
- Per-word: Arabic encouragement word + letter burst animation
- Lesson complete: animated gold arch draws itself, wheat sheaves, lesson stats, % comprehension gained
- Milestone complete (5% band): larger celebration, badge awarded, XP summary

---

## Screens (Full List)

### Existing (Keep As-Is)
1. **Onboarding** -- 3 slides including "high-frequency shortcut" pitch with animated comprehension bar
2. **Home** -- greeting, streak counter, "today's lesson" card (REPLACE surah library with milestone map -- see below)
3. **Learning Loop** -- Introduce, Recall, Pick Arabic, Pick English, Fill-in-the-blank
4. **Per-word celebration overlay**
5. **Lesson complete celebration**
6. **Progress screen** -- streak, weekly timeline, comprehension bars
7. **Settings** -- Arabic text size, transliteration on/off, pronunciation audio on/off

### New / Changed

**Home screen -- replace surah library with Milestone Map:**
- Visual path/ladder showing all 15 milestone bands (5% each)
- Current position highlighted
- Completed bands shown as filled/unlocked
- Next band shows lessons remaining
- "Start Today's Lesson" CTA prominent

**Ayah Context screen (new):**
- Full verse in Arabic with target word highlighted
- English translation below
- Surah/verse reference
- Audio play button for the highlighted word
- "Next word" or "Finish lesson" CTA

**Root Family view (new, accessible from any word card):**
- Show the root (3-letter Arabic root)
- List all words from that root the user has learned or will learn
- Tap any word to see its card

**"You can understand this now" moment (new):**
- Triggered after completing a short lesson
- Shows a full real verse using only words the user has already mastered (highlighted)
- With translation, surah reference, and celebration copy

---

## Design Improvements to Implement

1. **Milestone map as hero of home screen** -- visual path/ladder of 15 bands instead of flat surah list
2. **Per-word audio affordance** -- small speaker icon on every Arabic word on introduce card, recall card, and ayah context
3. **Root-family view** -- tapping a root reveals other words from the same root the user has learned or will learn
4. **"You can understand this now" moments** -- periodically show a full real verse the user can now read as a payoff
5. **Resume / "tired? stop here" affordance** -- explicit save point between short lessons so stopping feels safe and progress is never lost
6. **Accessibility pass** -- larger tap targets, contrast check on gold-on-navy, reduced-motion option

---

## Revision Tab (NEW - 2026-06-23)

Prototype file: `ayah-revision.html` (full clone of the app, prefilled with 2 lessons complete so the feature can be tried immediately).

### When it appears
A fourth bottom navigation tab, **Revise**, appears after at least one lesson is complete. It sits between Learn and Progress and is hidden until the first lesson is done. The tab pulls only from words the learner has already mastered, so it never tests unseen vocabulary.

### Three exercise modes
1. **Word Recognition** - Shows an Arabic word from completed lessons and the learner selects the correct English translation from four options. Runs as a short set (up to 8 words), then shows a score.
2. **Typing Exercise** - Shows the Arabic word and the learner types the English meaning, reinforcing active recall. Answer-matching is forgiving: it accepts any one of the slash-separated meanings (for example "from" or "of" for "from / of"), ignores case, extra spaces, and punctuation, and strips leading articles. Shows the accepted answer when wrong.
3. **Timed Challenge** - A 30-second timer with rapid Arabic-to-English multiple choice. Each answer advances immediately. At zero the learner sees how many they got correct and their accuracy.

### Notes for later integration into ayah.html
- The revision data source is `App.prototype.learnedPool()` (all words where `data.mastered[rank]` exists).
- Visibility is gated by `App.prototype.lessonsCompletedCount() > 0` inside `renderNav`.
- During an active quiz the bottom nav is hidden (flag `this.reviseActive`), restored on exit.
- All quiz screens reuse the existing gold/navy/green palette, Scheherazade New for Arabic, Poppins for UI, and the existing `ay-cta` / `ay-ghost` button styles. No new colors or fonts were introduced.

---

## Lesson Enhancements (NEW - 2026-06-23)

Prototype file: `ayah-lessons-enhanced.html` (full clone of the app, prefilled with 2 lessons complete).

### Quiz after each lesson (from the second lesson onward)
After the fill-in-the-blank step, lessons from th

---

## ayah.html Direct Updates (NEW - 2026-06-23, round 2)

These nine fixes were applied directly to the main `ayah.html` (search the code comments for context):

1. Context and fill now prefer SHORTER verses (`App.prototype.bestVerse`), and the word being translated is shown in a distinct colour with an underline, both in Arabic and in its English meaning. The rest of the ayah stays one colour.
2. The context screen says "You can now understand this full ayah" only when every word in the shown verse is known, otherwise "You can now partially understand this ayah".
3. On the Introduce card the Arabic word is centred and the audio icon sits directly below it.
4 and 6 and 8. Audio is fixed for every word, not just the examples. Root causes were: a fixed-timer autoplay that fired before the verse loaded, and a fallback that built the audio URL from the word position (Quran.com audio file numbers do not equal word positions). The app now always uses the verse's authoritative per-word `audio_url` for an exact match, and if a word's reference verse does not actually contain it, `bestVerse` searches for a short verse that does (preferring the exact vowelled form so pronunciation matches).
5. The recall flip card button "I knew it" is now "I am good to go". "Still learning" keeps the card on the meaning, does not advance, and re-queues the word for more practice.
7. The fill-in-the-blank verse is shorter and shows "Pick the word that means <meaning>" with the meaning underlined, so it is clear which word is being asked.
9. After every lesson a mastery check runs over all of the lesson's words; any word answered incorrectly is re-queued and the lesson will not finish until every word is answered correctly.

---

## Engagement Prototype in ayah-revision.html (NEW - 2026-06-23, round 3)

ayah-revision.html was rebuilt on top of the fixed ayah.html (all audio and lesson fixes included) and now layers Duolingo-style engagement features for trial, on top of the existing Revision tab:

- Lesson tip card: before a lesson, a short note on a shared root the words have in common.
- In-lesson combo counter: a streak indicator that builds on consecutive correct answers.
- New exercise types: tap-the-pairs (matching grid), build-the-sentence (arrange a real verse from its words), listen-and-choose (audio first), and type-the-meaning.
- Missed-only end review: only words missed during the lesson are drilled at the end, and the lesson will not finish until those are correct.
- Lesson levels and crowns: completing a lesson raises its level. Replaying a completed lesson (Strengthen) uses a harder exercise mix (fewer hints, audio first, typing). Crowns are shown on the home Practice card.
- Practice (spaced repetition): a home Practice card pulls words that are due, with a Simulate next day control to demonstrate the spacing. Practiced words are scheduled to return in two days.
- Session summary: an end-of-lesson recap with stats and a concrete goal nudge before the celebration.
- No punitive mechanics (no hearts or public leaderboards), keeping the encouraging tone.

The lesson flow is now: tip -> learn -> tap the pairs -> verse in context -> build the sentence -> fill in the blank -> mastery check of missed words -> summary -> celebration.
