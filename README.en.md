# AtomicWubi - Atomic Wubi Input Method Practice Platform

An elegant web application designed for practicing and learning the Wubi input method, helping users improve their typing speed and accuracy.

## Project Overview

AtomicWubi (Atomic Wubi) is a practice platform specifically designed for learners of the Wubi input method. It features a modern interface and gamified learning mechanics to make Wubi practice more engaging and efficient.

## Features

### 🎯 Core Features

- **Multiple Practice Modes**
  - Character Practice Mode: Targeted training with characters of varying difficulty levels
  - Article Practice Mode: Type full articles while learning simultaneously
  - Phrase Practice: Reinforce skills in inputting Wubi phrases

- **Learning Progress Tracking**
  - Daily XP statistics to record daily learning activity
  - Count of mastered characters to track personal progress
  - Streak counter to encourage daily practice habits

- **Gamification Mechanisms**
  - Achievement System: Earn achievements upon reaching specific milestones
  - Score System: Points awarded based on accuracy and typing speed
  - Combo System: Accumulate combo counts through consecutive correct inputs

### ⚙️ Settings Features

- **Sound Effects Toggle**: Enable or disable typing audio feedback
- **Blind Typing Mode**: Hide root hints to test true proficiency
- **Personal Preferences**: Save user-specific settings

### 📊 Data Statistics

- Total learning days
- Total accumulated XP
- Number of mastered characters
- Historical performance records

## Technology Stack

- **Frontend Framework**: Native JavaScript + HTML5 + CSS3
- **Data Storage**: LocalStorage
- **Audio System**: HTML5 Audio API

## Usage Instructions

### Quick Start

1. Open the `index.html` file
2. Log in with a username (or use default settings)
3. Select a practice mode to begin learning

### Keyboard Controls

- Type the corresponding Wubi code for each character
- Press `Enter` to confirm input
- Press `Esc` to exit the current screen

### Interface Navigation

| Interface | Description |
|-----------|-------------|
| Login Page | Enter your username to start learning |
| Home Page | View overview and access all modules |
| Level Selection | Choose practice difficulty levels |
| Practice Page | Character or article typing practice |
| Results Page | View performance metrics for this session |
| Wordbook Page | Manage custom practice word lists |
| Dictionary Page | Lookup Wubi codes |

## Project Structure

```
atomicwubi/
├── index.html          # Main application file
├── sounds/             # Sound effects directory
│   ├── correct.mp3     # Correct input sound
│   └── wrong.mp3       # Incorrect input sound
└── *.md                # Development documentation
```

## Sound Effects

To enable the full audio system, place the following files in the `sounds/` directory:

- `correct.mp3` - Sound played upon correct input
- `wrong.mp3` - Sound played upon incorrect input

You may obtain these sounds from free sound effect websites or record them yourself.

## Browser Support

- Chrome 80+
- Firefox 75+
- Safari 13+
- Edge 80+

## Future Plans

- 📈 Visualized training statistics and charts
- 🎵 Enhanced sound effect library
- 📱 Mobile device compatibility
- 🔥 Leaderboard functionality
- 👥 Multiplayer battle mode

## License

This project is intended solely for learning and educational purposes.

---

Wishing you continuous improvement in your Wubi typing skills! 🚀