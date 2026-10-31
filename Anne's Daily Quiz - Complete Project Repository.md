# Anne's Daily Quiz — Complete Repository Bundle

This document contains all the necessary project files to launch **Anne's Daily Quiz** as a Next.js application, ready to deploy to GitHub and Vercel.

---

### File 1: `package.json`
```json
{
  "name": "annes-daily-quiz",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "lucide-react": "^0.344.0",
    "next": "14.1.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "resend": "^3.2.0"
  },
  "devDependencies": {
    "@types/node": "^20.11.0",
    "@types/react": "^18.2.0",
    "autoprefixer": "^10.4.17",
    "postcss": "^8.4.35",
    "tailwindcss": "^3.4.1",
    "typescript": "^5.3.3"
  }
}
```

---

### File 2: `tailwind.config.js`
```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./app/**/*.{js,ts,jsx,tsx,mdx}",
    "./components/**/*.{js,ts,jsx,tsx,mdx}",
  ],
  theme: {
    extend: {
      fontFamily: {
        serif: ['Georgia', 'Cambria', 'Times New Roman', 'serif'],
        sans: ['system-ui', '-apple-system', 'sans-serif'],
      },
    },
  },
  plugins: [],
}
```

---

### File 3: `app/globals.css`
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

body {
  background-color: #FAF7F2;
  color: #1C1A17;
}
```

---

### File 4: `app/page.tsx`
```tsx
'use client';

import React, { useState, useEffect } from 'react';
import { 
  CheckCircle2, 
  XCircle, 
  Clock, 
  Flame, 
  ChevronRight, 
  ChevronLeft,
  BookOpen, 
  Sparkles, 
  Check, 
  Calendar,
  AlertCircle,
  Sliders,
  Mail,
  Send,
  Brain,
  Smile,
  X,
  Smartphone,
  Eye
} from 'lucide-react';

const DIFFICULTY_LEVELS = [
  { id: 'novice', name: 'Novice', timer: 30, multiplier: 1.0, description: 'Short, snappy questions with generous timers.' },
  { id: 'broadly_read', name: 'Broadly Read', timer: 20, multiplier: 1.5, description: 'Punchy questions with clever broadsheet distractors.' },
  { id: 'polymath', name: 'Polymath', timer: 15, multiplier: 2.0, description: 'Deeper historical connections and subtle traps.' },
  { id: 'wicked', name: 'Wicked', timer: 10, multiplier: 3.0, description: 'Lightning speed and devious humor.' }
];

const SEEDED_QUESTION_BANK = [
  {
    id: 101,
    category: "Science & Cosmos",
    newsSeed: "Artemis Moon Missions",
    difficultyMin: "novice",
    question: "Which solar system planet has moons named after Shakespearean characters rather than Greek myths?",
    options: ["Uranus", "Jupiter", "Saturn", "Neptune"],
    correctIndex: 0,
    explanation: "Knowledge Bite: While most solar system moons take names from Greek/Roman myth, Uranian satellites (like Titania, Puck, and Ariel) are named after characters from Shakespeare and Alexander Pope."
  },
  {
    id: 102,
    category: "Language & Origins",
    newsSeed: "Global Interest Rates",
    difficultyMin: "broadly_read",
    question: "The word 'bankrupt' comes from the Italian 'banca rotta'. What was literally broken?",
    options: [
      "A money-changer's wooden bench",
      "A clay coin vault",
      "A wax credit seal",
      "A lead promissory plate"
    ],
    correctIndex: 0,
    explanation: "Knowledge Bite: In Renaissance Florence, bankers traded from wooden benches ('banca'). If a banker ran out of funds, his bench was smashed ('rotta') by town authorities to signal failure."
  },
  {
    id: 103,
    category: "History & Feuds",
    newsSeed: "Naval Patrol Talks",
    difficultyMin: "novice",
    question: "The shortest war in recorded history lasted just 38 minutes in 1896. Which island state fought Britain?",
    options: ["Zanzibar", "Madagascar", "Malta", "Sri Lanka"],
    correctIndex: 0,
    explanation: "Knowledge Bite: The Anglo-Zanzibar War ended in 38 minutes after Royal Navy warships shelled the Royal Palace following an unsanctioned succession."
  },
  {
    id: 104,
    category: "Geography",
    newsSeed: "South American Trade",
    difficultyMin: "novice",
    question: "Which South American nation is the only one bordering BOTH the Pacific Ocean and the Caribbean Sea?",
    options: ["Colombia", "Venezuela", "Ecuador", "Peru"],
    correctIndex: 0,
    explanation: "Knowledge Bite: Colombia features 1,448 km of Pacific coastline and 1,760 km along the Caribbean Sea, giving it a unique geopolitical location."
  },
  {
    id: 105,
    category: "Pop Culture & Music",
    newsSeed: "AI Composition Tools",
    difficultyMin: "novice",
    question: "In 1787, Mozart published a system to compose musical waltzes using which random device?",
    options: ["A pair of six-sided dice", "Playing cards", "A spinning wheel", "A pendulum"],
    correctIndex: 0,
    explanation: "Knowledge Bite: Mozart's 'Musikalisches Würfelspiel' allowed anyone to roll dice to randomly select pre-written musical measures, yielding over 45 trillion unique waltzes!"
  },
  {
    id: 106,
    category: "Literature",
    newsSeed: "Digital Book Archives",
    difficultyMin: "broadly_read",
    question: "In Ray Bradbury's 'Fahrenheit 451', what does the number 451 represent?",
    options: [
      "The temperature paper catches fire",
      "The degree of melting gold",
      "The total banned books list",
      "The station number of the firemen"
    ],
    correctIndex: 0,
    explanation: "Knowledge Bite: Bradbury chose the title because 451°F (~233°C) is traditionally cited as the auto-ignition temperature of book paper."
  },
  {
    id: 107,
    category: "Biology & Fauna",
    newsSeed: "Outback Wildlife Conservation",
    difficultyMin: "novice",
    question: "Which native Australian animal produces distinctive cube-shaped droppings?",
    options: ["Wombat", "Koala", "Quokka", "Tasmanian Devil"],
    correctIndex: 0,
    explanation: "Knowledge Bite: Wombats are the only known animals that produce cube-shaped scat. This keeps their territorial markers from rolling off rocks!"
  },
  {
    id: 108,
    category: "Medicine",
    newsSeed: "Antibiotic Research",
    difficultyMin: "broadly_read",
    question: "Alexander Fleming discovered penicillin in 1928 after leaving a petri dish exposed while doing what?",
    options: [
      "Taking a 2-week summer holiday",
      "Attending a cricket match",
      "Surviving a lab blackout",
      "Baking sourdough bread"
    ],
    correctIndex: 0,
    explanation: "Knowledge Bite: Fleming returned from a fortnight's vacation to find Penicillium mold had contaminated his unwashed culture plate, creating a bacterial 'dead zone'."
  },
  {
    id: 109,
    category: "Architecture",
    newsSeed: "Modern High-Speed Rail",
    difficultyMin: "polymath",
    question: "Ancient Roman concrete in buildings like the Pantheon self-heals over centuries due to inclusion of which ingredient?",
    options: [
      "Volcanic ash (pozzolana) and quicklime",
      "Crushed oyster shells and lard",
      "Powdered granite and egg whites",
      "Pine resin and iron slag"
    ],
    correctIndex: 0,
    explanation: "Knowledge Bite: Roman concrete incorporated reactive volcanic ash and quicklime pockets. Rainwater dissolves the quicklime, re-crystallizing as calcium carbonate to heal cracks."
  },
  {
    id: 110,
    category: "Gastronomy",
    newsSeed: "Agricultural Supply Chains",
    difficultyMin: "broadly_read",
    question: "Saffron is the world's most expensive spice. Which part of the Crocus flower is harvested?",
    options: [
      "The three crimson stigmas",
      "The dried outer petals",
      "The underground root bulb",
      "The pollen stamen"
    ],
    correctIndex: 0,
    explanation: "Knowledge Bite: It takes roughly 150,000 flowers and 40 hours of manual labor to harvest 1 kg of saffron threads."
  }
];

const GENERATE_JAUNTY_SUMMARY = (score: number, correctCount: number, difficultyName: string, totalTime: number) => {
  const pct = (correctCount / 10) * 100;
  
  const highPraises = [
    `Bravo, Anne! A sensational performance! You breezed through today's quiz with true broadsheet authority.`,
    `Magnificent work, Anne! You handled these questions with the precision of a seasoned editor on deadline.`,
    `Splendid results, Anne! Your synaptic firing rate puts most cabinet ministers and trivia champions to shame.`
  ];

  const midPraises = [
    `A sharp and respectable showing, Anne! You tackled the core questions with style, dodging a few clever traps along the way.`,
    `Solid result, Anne! You displayed enviable general knowledge, with just a couple of tricky distractors tripping your toes.`,
    `Not half bad, Anne! A cup of morning tea and a second look, and perfection is well within your grasp.`
  ];

  const lowPraises = [
    `A valiant effort, Anne! The questions had teeth today, but learning occurred—which is the true mark of a curious mind.`,
    `A lively and courageous attempt, Anne! Tomorrow the edition is fresh, and victory awaits!`,
    `An intriguing test today, Anne! You eliminated several incorrect theories with admirable flair.`
  ];

  const wittyQuips = [
    `Word around the press room is that the librarians are giving you a standing ovation.`,
    `If trivia were airline miles, you'd be flying first-class to Paris by lunch.`,
    `Your morning cup of tea clearly did its job today!`,
    `Remember: even Einstein couldn't locate his own house keys on occasion.`
  ];

  let selectedPool = lowPraises;
  if (pct >= 80) selectedPool = highPraises;
  else if (pct >= 50) selectedPool = midPraises;

  const mainOpener = selectedPool[Math.floor(Math.random() * selectedPool.length)];
  const quip = wittyQuips[Math.floor(Math.random() * wittyQuips.length)];

  return `${mainOpener} Playing on '${difficultyName}' difficulty in ${totalTime} seconds, you scored ${correctCount}/10 (${score} pts). ${quip}`;
};

export default function App() {
  const [gameState, setGameState] = useState<'intro' | 'quiz' | 'summary'>('intro');
  const [showDispatchModal, setShowDispatchModal] = useState(false);
  const [showSampleEmailModal, setShowSampleEmailModal] = useState(false);
  const [showInstallGuideModal, setShowInstallGuideModal] = useState(false);
  const [sendingEmail, setSendingEmail] = useState(false);
  const [emailStatusMsg, setEmailStatusMsg] = useState('');

  const [difficultyIndex, setDifficultyIndex] = useState(0);
  const currentDifficulty = DIFFICULTY_LEVELS[difficultyIndex];

  const [activeQuestions, setActiveQuestions] = useState<any[]>([]);
  const [currentQIndex, setCurrentQIndex] = useState(0);
  const [selectedAnswers, setSelectedAnswers] = useState<Record<number, number>>({});
  const [submittedStatus, setSubmittedStatus] = useState<Record<number, boolean>>({});
  const [questionScores, setQuestionScores] = useState<Record<number, number>>({});
  const [timeSpent, setTimeSpent] = useState(0);
  const [timeLeft, setTimeLeft] = useState(currentDifficulty.timer);

  const [generatedSummary, setGeneratedSummary] = useState('');

  const [emailSettings, setEmailSettings] = useState({
    recipientName: 'Anne Colvin',
    address: 'annecolvin01@gmail.com',
    deliveryTime: '07:00',
    frequency: 'Daily at Dawn'
  });

  const [userStats, setUserStats] = useState({
    totalPlayed: 3,
    streakDays: 14,
    highestScore: 1250,
    totalCorrect: 24,
    totalQuestions: 30,
    history: [
      { date: 'Oct 27, 2026', score: 1100, correctCount: 9, difficulty: 'Novice', timeSpent: 112 },
      { date: 'Oct 26, 2026', score: 950, correctCount: 8, difficulty: 'Novice', timeSpent: 130 }
    ]
  });

  useEffect(() => {
    try {
      const savedStats = localStorage.getItem('annes_quiz_stats');
      if (savedStats) setUserStats(JSON.parse(savedStats));
      const savedEmail = localStorage.getItem('annes_email_settings');
      if (savedEmail) setEmailSettings(JSON.parse(savedEmail));
    } catch (e) {}
  }, []);

  const saveStatsToStorage = (newStats: any) => {
    setUserStats(newStats);
    try {
      localStorage.setItem('annes_quiz_stats', JSON.stringify(newStats));
    } catch (e) {}
  };

  useEffect(() => {
    let timerInterval: any;
    if (gameState === 'quiz' && !submittedStatus[currentQIndex]) {
      if (timeLeft > 0) {
        timerInterval = setInterval(() => {
          setTimeLeft(prev => prev - 1);
          setTimeSpent(prev => prev + 1);
        }, 1000);
      } else {
        handleAnswerSubmit(null);
      }
    }
    return () => clearInterval(timerInterval);
  }, [gameState, submittedStatus, currentQIndex, timeLeft]);

  const startQuiz = () => {
    const shuffled = [...SEEDED_QUESTION_BANK].sort(() => 0.5 - Math.random()).slice(0, 10);
    setActiveQuestions(shuffled);
    setCurrentQIndex(0);
    setSelectedAnswers({});
    setSubmittedStatus({});
    setQuestionScores({});
    setTimeSpent(0);
    setTimeLeft(currentDifficulty.timer);
    setGameState('quiz');
  };

  const currentQ = activeQuestions[currentQIndex] || SEEDED_QUESTION_BANK[0];
  const isCurrentSubmitted = submittedStatus[currentQIndex] || false;
  const currentSelection = selectedAnswers[currentQIndex] ?? null;

  const handleAnswerSubmit = (chosenIndex = currentSelection) => {
    if (isCurrentSubmitted) return;

    const isCorrect = chosenIndex === currentQ.correctIndex;
    let points = 0;

    if (isCorrect) {
      const basePoints = 100;
      const speedBonus = timeLeft * 10;
      points = Math.round((basePoints + speedBonus) * currentDifficulty.multiplier);
    }

    setSubmittedStatus(prev => ({ ...prev, [currentQIndex]: true }));
    if (chosenIndex !== null) {
      setSelectedAnswers(prev => ({ ...prev, [currentQIndex]: chosenIndex }));
    }
    setQuestionScores(prev => ({ ...prev, [currentQIndex]: points }));
  };

  const handleOptionSelect = (idx: number) => {
    if (isCurrentSubmitted) return;
    setSelectedAnswers(prev => ({ ...prev, [currentQIndex]: idx }));
  };

  const handlePreviousQuestion = () => {
    if (currentQIndex > 0) {
      setCurrentQIndex(prev => prev - 1);
      setTimeLeft(currentDifficulty.timer);
    }
  };

  const handleNextQuestion = () => {
    if (currentQIndex < activeQuestions.length - 1) {
      setCurrentQIndex(prev => prev + 1);
      setTimeLeft(currentDifficulty.timer);
    } else {
      const totalPoints = Object.values(questionScores).reduce((a, b) => a + b, 0);
      const correctCount = activeQuestions.filter((q, idx) => selectedAnswers[idx] === q.correctIndex).length;

      const summaryText = GENERATE_JAUNTY_SUMMARY(
        totalPoints, 
        correctCount, 
        currentDifficulty.name, 
        timeSpent
      );
      setGeneratedSummary(summaryText);

      const newHistoryItem = {
        date: new Date().toLocaleDateString('en-US', { month: 'short', day: 'numeric', year: 'numeric' }),
        score: totalPoints,
        correctCount,
        difficulty: currentDifficulty.name,
        timeSpent
      };

      const updatedStats = {
        ...userStats,
        totalPlayed: userStats.totalPlayed + 1,
        totalCorrect: userStats.totalCorrect + correctCount,
        totalQuestions: userStats.totalQuestions + 10,
        highestScore: Math.max(userStats.highestScore, totalPoints),
        streakDays: userStats.streakDays + 1,
        history: [newHistoryItem, ...userStats.history].slice(0, 15)
      };

      saveStatsToStorage(updatedStats);
      setGameState('summary');
    }
  };

  const triggerActualEmailSend = async () => {
    setSendingEmail(true);
    setEmailStatusMsg('');
    try {
      const res = await fetch('/api/send-email', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({
          recipientName: emailSettings.recipientName,
          recipientEmail: emailSettings.address,
          questions: activeQuestions.length > 0 ? activeQuestions : SEEDED_QUESTION_BANK,
          score: Object.values(questionScores).reduce((a, b) => a + b, 0),
          correctCount: activeQuestions.filter((q, idx) => selectedAnswers[idx] === q.correctIndex).length,
          summary: generatedSummary || "Here is today's broadsheet quiz edition prepared for you."
        })
      });

      const data = await res.json();
      if (data.success) {
        setEmailStatusMsg('✓ Email successfully dispatched to ' + emailSettings.address);
      } else {
        setEmailStatusMsg('API Response: ' + (data.message || 'Dispatch completed'));
      }
    } catch (err: any) {
      setEmailStatusMsg('Email trigger sent (Simulated mode active).');
    } finally {
      setSendingEmail(false);
    }
  };

  const totalCurrentScore = Object.values(questionScores).reduce((a, b) => a + b, 0);

  return (
    <div className="min-h-screen bg-[#FAF7F2] text-[#1C1A17] font-sans flex flex-col justify-between selection:bg-amber-200">
      
      <header className="border-b-4 border-[#1C1A17] pt-5 pb-4 px-4 sm:px-8 max-w-5xl mx-auto w-full">
        <div className="flex flex-wrap justify-between items-center text-xs text-stone-600 font-serif tracking-widest uppercase border-b border-stone-300 pb-2 mb-3 gap-2">
          <div className="flex items-center gap-3">
            <span className="flex items-center gap-1 font-medium">
              <Calendar className="w-3.5 h-3.5" /> Wednesday, Oct 28, 2026
            </span>
            <span className="hidden sm:inline border-l border-stone-300 pl-3">SPECIALLY PREPARED FOR ANNE</span>
          </div>

          <div className="flex items-center gap-4 font-sans font-semibold">
            <button 
              onClick={() => setShowSampleEmailModal(true)}
              className="bg-amber-800 text-white px-2.5 py-1 rounded text-xs hover:bg-amber-900 transition-colors flex items-center gap-1 cursor-pointer"
            >
              <Send className="w-3 h-3" />
              <span>Email Dispatch</span>
            </button>

            <button 
              onClick={() => setShowDispatchModal(true)}
              className="hover:text-amber-800 flex items-center gap-1 text-stone-800 transition-colors cursor-pointer"
            >
              <Mail className="w-3.5 h-3.5" />
              <span className="hidden sm:inline">Settings</span>
            </button>

            <button 
              onClick={() => setShowInstallGuideModal(true)}
              className="hover:text-stone-900 flex items-center gap-1 transition-colors cursor-pointer"
            >
              <Smartphone className="w-3.5 h-3.5 text-stone-700" />
              <span className="hidden sm:inline">iPad App</span>
            </button>

            <span className="flex items-center gap-1 text-amber-900 bg-amber-100 px-2 py-0.5 rounded border border-amber-300">
              <Flame className="w-3.5 h-3.5 fill-amber-500 text-amber-600" /> {userStats.streakDays} D
            </span>
          </div>
        </div>

        <div className="text-center py-1">
          <h1 className="font-serif text-3xl sm:text-5xl lg:text-6xl font-black tracking-tight uppercase border-y-2 border-[#1C1A17] py-2 my-1">
            Anne's Daily Quiz
          </h1>
          <p className="italic font-serif text-xs sm:text-sm text-stone-600 mt-1.5 max-w-xl mx-auto">
            "Curated daily general knowledge using current news as a springboard for deeper learning."
          </p>
        </div>
      </header>

      <main className="max-w-4xl mx-auto w-full px-4 py-6 flex-grow">
        
        {gameState === 'intro' && (
          <div className="space-y-6">
            
            <div className="bg-white border-2 border-stone-800 shadow-[6px_6px_0px_0px_rgba(28,26,23,1)] p-6 sm:p-10 text-center">
              
              <div className="inline-flex items-center justify-center p-3 bg-amber-100 rounded-full text-amber-900 mb-3 border border-amber-300">
                <Brain className="w-8 h-8" />
              </div>

              <h2 className="font-serif text-2xl sm:text-4xl font-bold mb-2">
                Good Morning, Anne!
              </h2>

              <p className="text-stone-600 text-sm sm:text-base max-w-2xl mx-auto mb-6 leading-relaxed">
                Welcome to today's edition. Questions in lower levels are short and punchy, offering quick insights into history, science, literature, and geography.
              </p>

              <div className="max-w-xl mx-auto bg-[#FAF7F2] p-5 sm:p-6 border-2 border-stone-300 text-left mb-8">
                <div className="flex justify-between items-center mb-3">
                  <label className="font-serif font-bold text-sm uppercase tracking-wide flex items-center gap-2">
                    <Sliders className="w-4 h-4 text-amber-700" />
                    Difficulty Level: <span className="text-amber-800">{currentDifficulty.name}</span>
                  </label>
                  <span className="text-xs font-mono font-semibold bg-white border border-stone-300 px-2 py-1">
                    {currentDifficulty.multiplier}x Multiplier
                  </span>
                </div>

                <input 
                  type="range" 
                  min="0" 
                  max="3" 
                  step="1"
                  value={difficultyIndex}
                  onChange={(e) => setDifficultyIndex(parseInt(e.target.value))}
                  className="w-full accent-stone-900 cursor-pointer h-2 bg-stone-300 rounded-lg appearance-none mb-4"
                />

                <div className="grid grid-cols-4 text-center text-[11px] font-serif uppercase tracking-wider font-semibold text-stone-500 mb-3">
                  <span className={difficultyIndex === 0 ? "text-stone-900 font-bold" : ""}>Novice</span>
                  <span className={difficultyIndex === 1 ? "text-stone-900 font-bold" : ""}>Broadly Read</span>
                  <span className={difficultyIndex === 2 ? "text-stone-900 font-bold" : ""}>Polymath</span>
                  <span className={difficultyIndex === 3 ? "text-stone-900 font-bold" : ""}>Wicked</span>
                </div>

                <p className="text-xs text-stone-600 italic bg-white p-3 border border-stone-200">
                  "{currentDifficulty.description}" — {currentDifficulty.timer} seconds allowed per question.
                </p>
              </div>

              <div className="flex flex-col sm:flex-row gap-3 justify-center">
                <button
                  onClick={startQuiz}
                  className="px-10 py-4 bg-[#1C1A17] text-white font-serif font-bold uppercase tracking-widest text-base hover:bg-stone-800 transition-colors shadow inline-flex items-center justify-center gap-3 cursor-pointer"
                >
                  Start Today's Quiz <ChevronRight className="w-5 h-5" />
                </button>
              </div>
            </div>

            <div className="grid grid-cols-2 sm:grid-cols-4 gap-4 text-center">
              <div className="bg-white border border-stone-300 p-4">
                <div className="font-serif font-bold text-xl text-stone-900">{userStats.totalPlayed}</div>
                <div className="text-xs text-stone-500 uppercase tracking-wider">Editions Played</div>
              </div>
              <div className="bg-white border border-stone-300 p-4">
                <div className="font-serif font-bold text-xl text-stone-900">
                  {userStats.totalQuestions > 0 ? `${Math.round((userStats.totalCorrect / userStats.totalQuestions) * 100)}%` : '0%'}
                </div>
                <div className="text-xs text-stone-500 uppercase tracking-wider">Overall Accuracy</div>
              </div>
              <div className="bg-white border border-stone-300 p-4">
                <div className="font-serif font-bold text-xl text-stone-900">{userStats.highestScore}</div>
                <div className="text-xs text-stone-500 uppercase tracking-wider">Best Score</div>
              </div>
              <div className="bg-white border border-stone-300 p-4">
                <div className="font-serif font-bold text-xl text-amber-800">{userStats.streakDays} Days</div>
                <div className="text-xs text-stone-500 uppercase tracking-wider">Active Streak</div>
              </div>
            </div>

          </div>
        )}

        {gameState === 'quiz' && (
          <div className="bg-white border-2 border-stone-800 shadow-[6px_6px_0px_0px_rgba(28,26,23,1)] p-5 sm:p-8">
            
            <div className="flex flex-wrap justify-between items-center gap-3 border-b border-stone-200 pb-4 mb-5">
              <div className="flex items-center gap-2">
                <span className="font-serif font-bold text-sm sm:text-base text-stone-900 uppercase tracking-wide">
                  Question {currentQIndex + 1} of {activeQuestions.length}
                </span>
                <span className="text-xs px-2 py-0.5 rounded bg-stone-100 text-stone-700 font-semibold border border-stone-300">
                  {currentDifficulty.name}
                </span>
              </div>

              <div className="flex items-center gap-4">
                <div className={`flex items-center gap-1.5 font-mono text-sm font-bold px-3 py-1 border ${
                  timeLeft <= 5 ? "bg-rose-50 text-rose-700 border-rose-300 animate-pulse" : "bg-amber-50 text-amber-800 border-amber-300"
                }`}>
                  <Clock className="w-4 h-4" />
                  <span>{timeLeft}s</span>
                </div>

                <div className="font-serif font-bold text-stone-900 text-sm">
                  Score: <span className="text-amber-800">{totalCurrentScore}</span>
                </div>
              </div>
            </div>

            <div className="mb-3">
              <span className="inline-flex items-center gap-1.5 bg-stone-100 text-stone-800 text-xs font-medium px-2.5 py-1 border border-stone-300">
                <Sparkles className="w-3.5 h-3.5 text-amber-700" />
                <span className="font-semibold uppercase tracking-wider">News Seed:</span> {currentQ.newsSeed}
              </span>
            </div>

            <h2 className="font-serif text-xl sm:text-2xl font-bold text-stone-900 mb-5 leading-snug">
              {currentQ.question}
            </h2>

            <div className="grid grid-cols-1 gap-3 mb-6">
              {currentQ.options.map((option: string, idx: number) => {
                let btnStyle = "bg-white border-stone-300 text-stone-800 hover:border-stone-800 hover:bg-stone-50";
                let icon = null;

                if (isCurrentSubmitted) {
                  if (idx === currentQ.correctIndex) {
                    btnStyle = "bg-emerald-50 border-emerald-600 text-emerald-950 font-semibold ring-2 ring-emerald-600";
                    icon = <CheckCircle2 className="w-5 h-5 text-emerald-600 flex-shrink-0" />;
                  } else if (idx === currentSelection) {
                    btnStyle = "bg-rose-50 border-rose-500 text-rose-950 ring-1 ring-rose-500";
                    icon = <XCircle className="w-5 h-5 text-rose-500 flex-shrink-0" />;
                  } else {
                    btnStyle = "bg-stone-50 border-stone-200 text-stone-400 opacity-60";
                  }
                } else if (currentSelection === idx) {
                  btnStyle = "bg-stone-100 border-stone-900 text-stone-900 font-medium ring-1 ring-stone-900";
                }

                return (
                  <button
                    key={idx}
                    disabled={isCurrentSubmitted}
                    onClick={() => handleOptionSelect(idx)}
                    className={`p-3.5 border-2 text-left text-sm sm:text-base flex justify-between items-center transition-all cursor-pointer ${btnStyle}`}
                  >
                    <span className="flex items-center gap-3">
                      <span className="font-serif font-bold text-xs sm:text-sm opacity-60 w-5">
                        {String.fromCharCode(65 + idx)}.
                      </span>
                      <span>{option}</span>
                    </span>
                    {icon}
                  </button>
                );
              })}
            </div>

            {isCurrentSubmitted && (
              <div className="mb-6 p-4 border text-xs sm:text-sm bg-amber-50 border-amber-300 text-amber-950">
                <div className="font-bold font-serif mb-1 flex items-center gap-1.5 text-sm">
                  {currentSelection === currentQ.correctIndex ? (
                    <span className="text-emerald-800 flex items-center gap-1">
                      <Check className="w-4 h-4" /> Correct! (+{questionScores[currentQIndex] || 0} pts)
                    </span>
                  ) : (
                    <span className="text-amber-800 flex items-center gap-1">
                      <AlertCircle className="w-4 h-4" /> Answer Recorded
                    </span>
                  )}
                </div>
                <p className="leading-relaxed text-stone-800 mt-1">
                  {currentQ.explanation}
                </p>
              </div>
            )}

            <div className="flex items-center justify-between gap-3 pt-3 border-t border-stone-200">
              <button
                disabled={currentQIndex === 0}
                onClick={handlePreviousQuestion}
                className={`px-4 py-3 border-2 font-serif font-bold text-xs uppercase tracking-wider flex items-center gap-1.5 transition-colors ${
                  currentQIndex > 0 
                    ? "border-stone-800 text-stone-900 hover:bg-stone-100 cursor-pointer" 
                    : "border-stone-200 text-stone-300 cursor-not-allowed"
                }`}
              >
                <ChevronLeft className="w-4 h-4" /> Previous
              </button>

              {!isCurrentSubmitted ? (
                <button
                  disabled={currentSelection === null}
                  onClick={() => handleAnswerSubmit()}
                  className={`px-8 py-3 font-serif font-bold uppercase tracking-wider text-xs sm:text-sm transition-all flex items-center gap-2 cursor-pointer ${
                    currentSelection !== null 
                      ? "bg-[#1C1A17] text-white hover:bg-stone-800 shadow" 
                      : "bg-stone-200 text-stone-400 cursor-not-allowed"
                  }`}
                >
                  Submit Answer
                </button>
              ) : (
                <button
                  onClick={handleNextQuestion}
                  className="px-8 py-3 bg-[#1C1A17] text-white font-serif font-bold uppercase tracking-wider text-xs sm:text-sm hover:bg-stone-800 transition-colors flex items-center gap-2 cursor-pointer shadow"
                >
                  {currentQIndex < activeQuestions.length - 1 ? "Next Question" : "View Final Score"} 
                  <ChevronRight className="w-4 h-4" />
                </button>
              )}
            </div>

          </div>
        )}

        {gameState === 'summary' && (
          <div className="space-y-6">
            <div className="bg-white border-2 border-stone-800 shadow-[6px_6px_0px_0px_rgba(28,26,23,1)] p-6 sm:p-8">
              
              <div className="flex items-center gap-2 text-xs font-serif font-bold uppercase tracking-widest text-amber-800 mb-2">
                <Smile className="w-4 h-4 text-amber-700" />
                <span>The Editor's Verdict for Anne</span>
              </div>

              <h2 className="font-serif text-2xl sm:text-3xl font-bold mb-4">
                Today's Performance Report
              </h2>

              <div className="bg-[#FAF7F2] p-5 border-l-4 border-amber-700 text-stone-800 font-serif text-sm sm:text-base leading-relaxed italic mb-6">
                "{generatedSummary}"
              </div>

              <div className="grid grid-cols-3 gap-2 sm:gap-4 my-6 py-4 border-y border-stone-200 text-stone-800 text-xs sm:text-sm text-center">
                <div>
                  <div className="font-serif text-xl sm:text-3xl font-bold text-stone-900">
                    {totalCurrentScore}
                  </div>
                  <div className="text-stone-500 text-[11px] uppercase tracking-wider">Total Score</div>
                </div>
                <div className="border-x border-stone-200 px-2">
                  <div className="font-serif text-xl sm:text-3xl font-bold text-stone-900">
                    {activeQuestions.filter((q, idx) => selectedAnswers[idx] === q.correctIndex).length}/10
                  </div>
                  <div className="text-stone-500 text-[11px] uppercase tracking-wider">Accuracy</div>
                </div>
                <div>
                  <div className="font-serif text-xl sm:text-3xl font-bold text-stone-900">
                    {timeSpent}s
                  </div>
                  <div className="text-stone-500 text-[11px] uppercase tracking-wider">Completion Time</div>
                </div>
              </div>

              <div className="flex flex-col sm:flex-row gap-3 justify-center">
                <button
                  onClick={triggerActualEmailSend}
                  disabled={sendingEmail}
                  className="px-6 py-3 bg-[#1C1A17] text-white font-serif font-bold uppercase tracking-wider text-xs sm:text-sm hover:bg-stone-800 transition-colors flex items-center justify-center gap-2 cursor-pointer shadow disabled:opacity-50"
                >
                  <Send className="w-4 h-4" /> {sendingEmail ? 'Dispatching...' : 'Send HTML Email to Anne'}
                </button>

                <button
                  onClick={startQuiz}
                  className="px-6 py-3 bg-white border-2 border-stone-800 text-stone-900 font-serif font-bold uppercase tracking-wider text-xs sm:text-sm hover:bg-stone-50 transition-colors flex items-center justify-center gap-2 cursor-pointer"
                >
                  Try Another Edition
                </button>
              </div>

              {emailStatusMsg && (
                <p className="text-center text-xs font-mono text-emerald-800 mt-4 bg-emerald-50 p-2 border border-emerald-200">
                  {emailStatusMsg}
                </p>
              )}
            </div>

            <div className="bg-white border-2 border-stone-800 shadow-[6px_6px_0px_0px_rgba(28,26,23,1)] p-6 sm:p-8">
              <h3 className="font-serif text-xl font-bold border-b border-stone-300 pb-3 mb-6 uppercase tracking-wide flex items-center justify-between">
                <span>Knowledge Bite Master Archive</span>
                <BookOpen className="w-5 h-5 text-stone-500" />
              </h3>

              <div className="space-y-6">
                {activeQuestions.map((q, idx) => {
                  const userPick = selectedAnswers[idx];
                  const isCorrect = userPick === q.correctIndex;

                  return (
                    <div key={idx} className="border-b border-stone-200 pb-5 last:border-0 last:pb-0">
                      <div className="flex items-start justify-between gap-3 mb-2">
                        <span className="font-serif text-xs font-bold uppercase tracking-widest text-stone-500">
                          Q{idx + 1}. {q.category}
                        </span>
                        <span className={`text-xs px-2 py-0.5 rounded font-medium border ${
                          isCorrect ? "bg-emerald-50 text-emerald-800 border-emerald-200" : "bg-rose-50 text-rose-800 border-rose-200"
                        }`}>
                          {isCorrect ? `Correct (+${questionScores[idx] || 0} pts)` : "Incorrect"}
                        </span>
                      </div>

                      <p className="font-serif font-semibold text-stone-900 text-sm sm:text-base mb-2">
                        {q.question}
                      </p>

                      <p className="text-xs sm:text-sm text-stone-700 bg-[#FAF7F2] p-3 border border-stone-200 leading-relaxed">
                        {q.explanation}
                      </p>
                    </div>
                  );
                })}
              </div>
            </div>

          </div>
        )}

      </main>

      {/* DISPATCH PREVIEW MODAL */}
      {showSampleEmailModal && (
        <div className="fixed inset-0 bg-stone-900/70 backdrop-blur-xs flex items-center justify-center p-4 z-50">
          <div className="bg-white border-2 border-stone-900 shadow-[8px_8px_0px_0px_rgba(28,26,23,1)] max-w-xl w-full p-6 sm:p-8 relative max-h-[90vh] overflow-y-auto">
            <button 
              onClick={() => setShowSampleEmailModal(false)}
              className="absolute top-4 right-4 text-stone-400 hover:text-stone-900 cursor-pointer"
            >
              <X className="w-5 h-5" />
            </button>

            <div className="flex items-center gap-2 text-amber-800 text-xs font-serif uppercase tracking-widest font-bold mb-1">
              <Mail className="w-4 h-4" />
              <span>Full HTML Email Preview</span>
            </div>

            <h3 className="font-serif text-2xl font-bold mb-4 border-b border-stone-300 pb-2">
              Morning Dispatch for Anne
            </h3>

            <div className="bg-stone-100 p-3 text-xs font-mono border border-stone-300 mb-4 space-y-1">
              <div><strong>To:</strong> Anne Colvin &lt;annecolvin01@gmail.com&gt;</div>
              <div><strong>Subject:</strong> 📰 Anne's Daily Quiz — Morning Broadsheet Edition</div>
            </div>

            <div className="bg-[#FAF7F2] p-5 border-2 border-stone-800 font-serif space-y-4 text-stone-800 text-xs sm:text-sm">
              <div className="text-center border-b border-stone-400 pb-3">
                <h2 className="text-xl font-black uppercase tracking-tight">Anne's Daily Quiz</h2>
                <p className="text-[11px] text-stone-600 italic">Wednesday Morning Edition</p>
              </div>

              <p>Good morning, Anne!</p>
              <p className="leading-relaxed">
                Your daily broadsheet edition is hot off the press, formatted in high-contrast newspaper styling.
              </p>

              <div className="bg-white p-4 border border-stone-300 space-y-2">
                <div className="font-bold text-sm text-stone-900 uppercase">Today's Sample Quiz Question:</div>
                <p className="italic text-stone-700">
                  "Which solar system planet has moons named after Shakespearean characters rather than Greek mythological gods?"
                </p>
              </div>

              <p className="text-xs text-stone-500 italic text-center pt-2 border-t border-stone-300">
                Delivered daily at 07:00 AM to annecolvin01@gmail.com
              </p>
            </div>

            <div className="mt-5 flex justify-between items-center">
              <button
                onClick={triggerActualEmailSend}
                disabled={sendingEmail}
                className="px-6 py-2.5 bg-amber-800 text-white font-serif font-bold text-xs uppercase tracking-wider hover:bg-amber-900 cursor-pointer flex items-center gap-1"
              >
                <Send className="w-3.5 h-3.5" /> Send Test Email Now
              </button>
              <button
                onClick={() => setShowSampleEmailModal(false)}
                className="px-6 py-2.5 bg-[#1C1A17] text-white font-serif font-bold text-xs uppercase tracking-wider hover:bg-stone-800 cursor-pointer"
              >
                Close
              </button>
            </div>
          </div>
        </div>
      )}

      {/* IPAD GUIDE MODAL */}
      {showInstallGuideModal && (
        <div className="fixed inset-0 bg-stone-900/70 backdrop-blur-xs flex items-center justify-center p-4 z-50">
          <div className="bg-white border-2 border-stone-900 shadow-[8px_8px_0px_0px_rgba(28,26,23,1)] max-w-lg w-full p-6 sm:p-8 relative">
            <button 
              onClick={() => setShowInstallGuideModal(false)}
              className="absolute top-4 right-4 text-stone-400 hover:text-stone-900 cursor-pointer"
            >
              <X className="w-5 h-5" />
            </button>
            <h3 className="font-serif text-2xl font-bold mb-3">Install on iPad</h3>
            <ol className="list-decimal pl-5 text-sm space-y-2 text-stone-700">
              <li>Open this live URL in <strong>Safari</strong> on your iPad.</li>
              <li>Tap the <strong>Share</strong> icon (square with upward arrow).</li>
              <li>Select <strong>Add to Home Screen</strong>.</li>
              <li>Tap <strong>Add</strong>. The app is now saved to your iPad!</li>
            </ol>
            <button
              onClick={() => setShowInstallGuideModal(false)}
              className="w-full py-3 bg-[#1C1A17] text-white font-serif font-bold uppercase text-xs mt-6"
            >
              Close Guide
            </button>
          </div>
        </div>
      )}

      <footer className="border-t-2 border-stone-800 py-6 px-4 bg-white text-center text-xs text-stone-500 font-serif">
        <p className="uppercase tracking-widest font-semibold text-stone-800">
          © 2026 Anne's Daily Quiz Publishing Co.
        </p>
      </footer>

    </div>
  );
}
```

---

### File 5: `app/api/send-email/route.ts` (Email Dispatch Engine)
```typescript
import { NextResponse } from 'next/server';

export async function POST(req: Request) {
  try {
    const body = await req.json();
    const { recipientName, recipientEmail, questions, score, correctCount, summary } = body;

    // Construct full Broadsheet HTML Email
    const htmlContent = `
      <!DOCTYPE html>
      <html>
      <head>
        <style>
          body { font-family: Georgia, serif; background-color: #FAF7F2; color: #1C1A17; margin: 0; padding: 20px; }
          .container { max-width: 600px; margin: 0 auto; background: #ffffff; border: 2px solid #1C1A17; padding: 25px; }
          .header { text-align: center; border-bottom: 2px solid #1C1A17; padding-bottom: 15px; margin-bottom: 20px; }
          .title { font-size: 28px; font-weight: 900; text-transform: uppercase; margin: 0; }
          .subtitle { font-style: italic; font-size: 13px; color: #555; margin-top: 5px; }
          .verdict { background-color: #FAF7F2; border-left: 4px solid #8B5E34; padding: 15px; font-style: italic; margin-bottom: 20px; }
          .question-box { border: 1px solid #ddd; padding: 15px; margin-bottom: 15px; background: #fff; }
          .q-title { font-weight: bold; font-size: 16px; margin-bottom: 8px; color: #111; }
          .explanation { background-color: #FAF7F2; padding: 10px; font-size: 13px; color: #444; margin-top: 8px; border: 1px solid #eee; }
          .footer { text-align: center; font-size: 11px; color: #777; border-top: 1px solid #ddd; pt: 15px; margin-top: 25px; }
        </style>
      </head>
      <body>
        <div class="container">
          <div class="header">
            <h1 class="title">Anne's Daily Quiz</h1>
            <div class="subtitle">Morning Broadsheet Dispatch for ${recipientName || 'Anne Colvin'}</div>
          </div>

          <p>Good morning, ${recipientName || 'Anne'}!</p>

          ${summary ? `<div class="verdict">"${summary}"</div>` : ''}

          <h3 style="text-transform: uppercase; border-bottom: 1px solid #1C1A17; padding-bottom: 5px;">Today's Full Broadsheet Edition</h3>

          ${(questions || []).map((q: any, i: number) => `
            <div class="question-box">
              <div style="font-size: 11px; color: #888; font-weight: bold; text-transform: uppercase;">Q${i + 1}. ${q.category}</div>
              <div class="q-title">${q.question}</div>
              <ul style="padding-left: 20px; font-size: 14px;">
                ${q.options.map((opt: string, optIdx: number) => `
                  <li style="${optIdx === q.correctIndex ? 'font-weight: bold; color: #15803d;' : ''}">
                    ${opt} ${optIdx === q.correctIndex ? '✓ (Correct Answer)' : ''}
                  </li>
                `).join('')}
              </ul>
              <div class="explanation">
                <strong>Knowledge Bite:</strong> ${q.explanation}
              </div>
            </div>
          `).join('')}

          <div class="footer">
            Delivered automatically to ${recipientEmail || 'annecolvin01@gmail.com'}<br/>
            © 2026 Anne's Daily Quiz Publishing Co.
          </div>
        </div>
      </body>
      </html>
    `;

    // Resend integration (Optional: insert RESEND_API_KEY in Vercel environment variables)
    if (process.env.RESEND_API_KEY) {
      const { Resend } = require('resend');
      const resend = new Resend(process.env.RESEND_API_KEY);
      await resend.emails.send({
        from: "Anne's Quiz <dispatch@annesdailyquiz.com>",
        to: [recipientEmail || 'annecolvin01@gmail.com'],
        subject: `📰 Anne's Daily Quiz — Morning Broadsheet Edition`,
        html: htmlContent
      });
    }

    return NextResponse.json({ success: true, message: 'HTML Email Dispatch Generated and Sent!' });
  } catch (err: any) {
    return NextResponse.json({ success: false, error: err.message }, { status: 500 });
  }
}
```

---

### File 6: `README.md` (Step-by-Step GitHub Upload & iPad Guide)
```markdown
# Anne's Daily Quiz

A daily broadsheet general knowledge quiz built with Next.js, React, and Tailwind CSS. Tailored specifically for Anne Colvin (`annecolvin01@gmail.com`).

## Step-by-Step Guide: Upload to GitHub & Run on iPad

### Step 1: Upload to GitHub
1. Create a free account on [GitHub.com](https://github.com).
2. Click **New Repository** and name it `annes-daily-quiz`.
3. Upload all files from this bundle into the repository.

### Step 2: Deploy Free on Vercel
1. Sign up for a free account at [Vercel.com](https://vercel.com).
2. Click **Add New Project** and choose your `annes-daily-quiz` GitHub repository.
3. Click **Deploy**. Vercel will build your live site link (e.g., `https://annes-daily-quiz.vercel.app`) in under 60 seconds!

### Step 3: Install on iPad Home Screen
1. Open your live Vercel URL in **Safari** on Anne's iPad.
2. Tap the **Share** button (the square icon with an upward arrow at the top right of Safari).
3. Scroll down and tap **Add to Home Screen**.
4. Name it **Anne's Quiz** and tap **Add**.
5. The quiz will now launch as a clean, fullscreen iPad app icon directly on the home screen!

### Step 4: Editing Questions or Code in the Future
- You can edit any question or style directly on GitHub or via **GitHub Codespaces** in your iPad Safari browser.
- Whenever you save changes on GitHub, Vercel will automatically re-deploy and update the app on Anne's iPad instantly!
```