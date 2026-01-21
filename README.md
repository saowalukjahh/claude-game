import React, { useState, useEffect } from 'react';
import { Sparkles, Heart, Trophy, ArrowUp, ArrowDown, ArrowLeft, ArrowRight } from 'lucide-react';

const ZooAdventureGame = () => {
  const [playerPos, setPlayerPos] = useState({ x: 1, y: 8 });
  const [score, setScore] = useState(0);
  const [lives, setLives] = useState(3);
  const [currentQuestion, setCurrentQuestion] = useState(null);
  const [gameStatus, setGameStatus] = useState('playing');
  const [answeredQuestions, setAnsweredQuestions] = useState([]);
  const [showFeedback, setShowFeedback] = useState(null);

  const questions = [
    {
      id: 1,
      position: { x: 5, y: 8 },
      emoji: '🦁',
      question: 'โลหะมีสมบัติข้อใดที่แตกต่างจากอโลหะ?',
      options: [
        'นำความร้อนได้ดี',
        'เปราะแตกง่าย',
        'ไม่มีความเงางาม',
        'เป็นฉนวนไฟฟ้า'
      ],
      correct: 0
    },
    {
      id: 2,
      position: { x: 8, y: 7 },
      emoji: '🐘',
      question: 'วัสดุใดต่อไปนี้เป็นโลหะ?',
      options: ['กำมะถัน', 'ทองแดง', 'คาร์บอน', 'ซิลิกอน'],
      correct: 1
    },
    {
      id: 3,
      position: { x: 8, y: 4 },
      emoji: '🦒',
      question: 'พลาสติกมีสมบัติข้อใดที่เป็นข้อเสีย?',
      options: [
        'เบาและทนทาน',
        'ราคาถูก',
        'ย่อยสลายได้ยาก',
        'ขึ้นรูปง่าย'
      ],
      correct: 2
    },
    {
      id: 4,
      position: { x: 5, y: 3 },
      emoji: '🐯',
      question: 'แก้วมีสมบัติพิเศษอะไร?',
      options: [
        'นำไฟฟ้าได้ดี',
        'โปร่งใสและผ่านแสงได้',
        'นิ่มและยืดหยุ่น',
        'เบามาก'
      ],
      correct: 1
    },
    {
      id: 5,
      position: { x: 2, y: 2 },
      emoji: '🦓',
      question: 'เซรามิกมีคุณสมบัติใด?',
      options: [
        'ทนความร้อนสูง',
        'นำไฟฟ้าได้ดี',
        'ยืดหยุ่นมาก',
        'เบากว่าน้ำ'
      ],
      correct: 0
    },
    {
      id: 6,
      position: { x: 2, y: 5 },
      emoji: '🐼',
      question: 'ไม้มีข้อดีในการใช้งานอะไร?',
      options: [
        'ทนความร้อนสูงมาก',
        'หาได้ง่ายและเป็นวัสดุธรรมชาติ',
        'ไม่ติดไฟ',
        'แข็งกว่าเหล็ก'
      ],
      correct: 1
    },
    {
      id: 7,
      position: { x: 5, y: 5 },
      emoji: '🦘',
      question: 'ยางมีสมบัติพิเศษอะไร?',
      options: [
        'แข็งมาก',
        'ยืดหยุ่นและกลับคืนรูปได้',
        'หนักมาก',
        'โปร่งใส'
      ],
      correct: 1
    },
    {
      id: 8,
      position: { x: 8, y: 1 },
      emoji: '🐨',
      question: 'โลหะผสมคืออะไร?',
      options: [
        'โลหะบริสุทธิ์',
        'การนำโลหะมาผสมกันเพื่อปรับปรุงคุณสมบัติ',
        'พลาสติกชนิดหนึ่ง',
        'วัสดุจากธรรมชาติ'
      ],
      correct: 1
    },
    {
      id: 9,
      position: { x: 1, y: 1 },
      emoji: '🦅',
      question: 'คอนกรีตมีคุณสมบัติอะไร?',
      options: [
        'นำไฟฟ้าได้ดี',
        'ยืดหยุ่นมาก',
        'แข็งแรงและทนทาน',
        'เบามาก'
      ],
      correct: 2
    },
    {
      id: 10,
      position: { x: 4, y: 1 },
      emoji: '🦜',
      question: 'การเลือกใช้วัสดุควรพิจารณาเรื่องอะไรเป็นหลัก?',
      options: [
        'ราคาถูกที่สุดเท่านั้น',
        'สวยงามที่สุด',
        'เหมาะสมกับการใช้งานและผลกระทบต่อสิ่งแวดล้อม',
        'หาซื้อง่ายที่สุด'
      ],
      correct: 2
    }
  ];

  const playCorrectSound = () => {
    const audioContext = new (window.AudioContext || window.webkitAudioContext)();
    const oscillator = audioContext.createOscillator();
    const gainNode = audioContext.createGain();
    
    oscillator.connect(gainNode);
    gainNode.connect(audioContext.destination);
    
    oscillator.frequency.value = 523.25;
    gainNode.gain.value = 0.3;
    oscillator.start();
    
    setTimeout(() => {
      oscillator.frequency.value = 659.25;
    }, 100);
    
    setTimeout(() => {
      oscillator.frequency.value = 783.99;
    }, 200);
    
    setTimeout(() => {
      gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.1);
      oscillator.stop();
    }, 300);
  };

  const playWrongSound = () => {
    const audioContext = new (window.AudioContext || window.webkitAudioContext)();
    const oscillator = audioContext.createOscillator();
    const gainNode = audioContext.createGain();
    
    oscillator.connect(gainNode);
    gainNode.connect(audioContext.destination);
    
    oscillator.frequency.value = 200;
    gainNode.gain.value = 0.2;
    oscillator.start();
    
    setTimeout(() => {
      gainNode.gain.exponentialRampToValueAtTime(0.01, audioContext.currentTime + 0.1);
      oscillator.stop();
    }, 200);
  };

  useEffect(() => {
    const question = questions.find(
      q => q.position.x === playerPos.x && q.position.y === playerPos.y && !answeredQuestions.includes(q.id)
    );
    
    if (question && gameStatus === 'playing') {
      setCurrentQuestion(question);
    }
  }, [playerPos, gameStatus]);

  const handleMove = (dx, dy) => {
    if (gameStatus !== 'playing' || currentQuestion) return;
    
    const newX = Math.max(1, Math.min(9, playerPos.x + dx));
    const newY = Math.max(1, Math.min(9, playerPos.y + dy));
    
    setPlayerPos({ x: newX, y: newY });
  };

  useEffect(() => {
    const handleKeyPress = (e) => {
      switch(e.key) {
        case 'ArrowUp': handleMove(0, -1); break;
        case 'ArrowDown': handleMove(0, 1); break;
        case 'ArrowLeft': handleMove(-1, 0); break;
        case 'ArrowRight': handleMove(1, 0); break;
      }
    };

    window.addEventListener('keydown', handleKeyPress);
    return () => window.removeEventListener('keydown', handleKeyPress);
  }, [playerPos, currentQuestion, gameStatus]);

  const handleAnswer = (selectedIndex) => {
    if (selectedIndex === currentQuestion.correct) {
      playCorrectSound();
      setScore(score + 1);
      setAnsweredQuestions([...answeredQuestions, currentQuestion.id]);
      setShowFeedback('correct');
      
      setTimeout(() => {
        setShowFeedback(null);
        setCurrentQuestion(null);
        
        if (score + 1 === 10) {
          setGameStatus('won');
        }
      }, 1500);
    } else {
      playWrongSound();
      const newLives = lives - 1;
      setLives(newLives);
      setShowFeedback('wrong');
      
      setTimeout(() => {
        setShowFeedback(null);
        setCurrentQuestion(null);
        
        if (newLives === 0) {
          setGameStatus('lost');
        }
      }, 1500);
    }
  };

  const resetGame = () => {
    setPlayerPos({ x: 1, y: 8 });
    setScore(0);
    setLives(3);
    setCurrentQuestion(null);
    setGameStatus('playing');
    setAnsweredQuestions([]);
    setShowFeedback(null);
  };

  const renderGrid = () => {
    const grid = [];
    for (let y = 1; y <= 9; y++) {
      for (let x = 1; x <= 9; x++) {
        const question = questions.find(q => q.position.x === x && q.position.y === y);
        const isAnswered = question && answeredQuestions.includes(question.id);
        const isPlayer = playerPos.x === x && playerPos.y === y;
        
        grid.push(
          <div
            key={`${x}-${y}`}
            className={`w-16 h-16 border-2 flex items-center justify-center text-3xl transition-all ${
              isPlayer ? 'bg-blue-200 border-blue-500 scale-110' : 'bg-green-50 border-green-300'
            } ${isAnswered ? 'opacity-30' : ''}`}
          >
            {isPlayer && '🧑‍🎓'}
            {question && !isAnswered && !isPlayer && question.emoji}
          </div>
        );
      }
    }
    return grid;
  };

  if (gameStatus === 'won') {
    return (
      <div className="flex flex-col items-center justify-center min-h-screen bg-gradient-to-b from-yellow-200 to-orange-300 p-8">
        <Trophy className="w-32 h-32 text-yellow-500 mb-4 animate-bounce" />
        <h1 className="text-5xl font-bold text-white mb-4">🎉 ยินดีด้วย! 🎉</h1>
        <p className="text-2xl text-white mb-8">คุณผ่านด่านทั้งหมดแล้ว!</p>
        <p className="text-xl text-white mb-8">คะแนน: {score}/10</p>
        <button
          onClick={resetGame}
          className="px-8 py-4 bg-white text-orange-600 rounded-lg text-xl font-bold hover:bg-orange-100 transition-colors"
        >
          เล่นอีกครั้ง
        </button>
      </div>
    );
  }

  if (gameStatus === 'lost') {
    return (
      <div className="flex flex-col items-center justify-center min-h-screen bg-gradient-to-b from-gray-400 to-gray-600 p-8">
        <h1 className="text-5xl font-bold text-white mb-4">😔 เกมจบ</h1>
        <p className="text-2xl text-white mb-8">คุณตอบผิด 3 ครั้งแล้ว</p>
        <p className="text-xl text-white mb-8">คะแนน: {score}/10</p>
        <button
          onClick={resetGame}
          className="px-8 py-4 bg-white text-gray-700 rounded-lg text-xl font-bold hover:bg-gray-200 transition-colors"
        >
          เล่นใหม่
        </button>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gradient-to-b from-blue-200 to-green-200 p-4">
      <div className="max-w-4xl mx-auto">
        <div className="bg-white rounded-lg shadow-lg p-6 mb-4">
          <h1 className="text-3xl font-bold text-center text-green-700 mb-2">
            🦁 เกมผจญภัยสวนสัตว์: สมบัติของวัสดุ 🦁
          </h1>
          <p className="text-center text-gray-600 mb-4">ระดับชั้น ม.4 - หนังสือออกแบบเทคโนโลยี สสวท.</p>
          
          <div className="flex justify-around items-center text-lg mb-4">
            <div className="flex items-center gap-2">
              <Sparkles className="text-yellow-500" />
              <span className="font-bold">คะแนน: {score}/10</span>
            </div>
            <div className="flex items-center gap-2">
              <Heart className="text-red-500" />
              <span className="font-bold">ชีวิต: {lives}/3</span>
            </div>
          </div>

          <div className="bg-blue-50 p-3 rounded text-center text-sm text-gray-700">
            ใช้ปุ่มลูกศร ⬆️⬇️⬅️➡️ หรือกดปุ่มด้านล่างเพื่อเดินไปหาสัตว์และตอบคำถาม
          </div>
        </div>

        <div className="bg-white rounded-lg shadow-lg p-4 mb-4">
          <div className="grid grid-cols-9 gap-1 mb-4">
            {renderGrid()}
          </div>

          <div className="grid grid-cols-4 gap-2 max-w-xs mx-auto">
            <div></div>
            <button
              onClick={() => handleMove(0, -1)}
              className="bg-blue-500 text-white p-3 rounded hover:bg-blue-600 flex items-center justify-center"
              disabled={currentQuestion || gameStatus !== 'playing'}
            >
              <ArrowUp />
            </button>
            <div></div>
            <div></div>
            <button
              onClick={() => handleMove(-1, 0)}
              className="bg-blue-500 text-white p-3 rounded hover:bg-blue-600 flex items-center justify-center"
              disabled={currentQuestion || gameStatus !== 'playing'}
            >
              <ArrowLeft />
            </button>
            <button
              onClick={() => handleMove(0, 1)}
              className="bg-blue-500 text-white p-3 rounded hover:bg-blue-600 flex items-center justify-center"
              disabled={currentQuestion || gameStatus !== 'playing'}
            >
              <ArrowDown />
            </button>
            <button
              onClick={() => handleMove(1, 0)}
              className="bg-blue-500 text-white p-3 rounded hover:bg-blue-600 flex items-center justify-center"
              disabled={currentQuestion || gameStatus !== 'playing'}
            >
              <ArrowRight />
            </button>
          </div>
        </div>

        {currentQuestion && (
          <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50">
            <div className="bg-white rounded-lg shadow-2xl p-8 max-w-2xl w-full">
              <div className="text-6xl text-center mb-4">{currentQuestion.emoji}</div>
              <h2 className="text-2xl font-bold text-center mb-6 text-gray-800">
                {currentQuestion.question}
              </h2>
              
              {showFeedback ? (
                <div className={`text-center py-8 ${showFeedback === 'correct' ? 'text-green-600' : 'text-red-600'}`}>
                  <div className="text-6xl mb-4">
                    {showFeedback === 'correct' ? '✅' : '❌'}
                  </div>
                  <div className="text-3xl font-bold">
                    {showFeedback === 'correct' ? 'ถูกต้อง! 🎉' : 'ผิด! 😢'}
                  </div>
                </div>
              ) : (
                <div className="space-y-3">
                  {currentQuestion.options.map((option, index) => (
                    <button
                      key={index}
                      onClick={() => handleAnswer(index)}
                      className="w-full p-4 text-left bg-blue-50 hover:bg-blue-100 rounded-lg border-2 border-blue-200 hover:border-blue-400 transition-all font-medium"
                    >
                      {String.fromCharCode(65 + index)}. {option}
                    </button>
                  ))}
                </div>
              )}
            </div>
          </div>
        )}
      </div>
    </div>
  );
};

export default ZooAdventureGame;
