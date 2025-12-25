# ielts-app
import React, { useState, useEffect, useRef } from 'react';
import { Volume2, ChevronLeft, ChevronRight, CheckCircle, RotateCcw, Award, GraduationCap, XCircle } from 'lucide-react';

// 模拟的雅思核心词汇数据
const INITIAL_VOCAB_DATA = [
  {
    id: 1,
    word: "Ambiguous",
    ipa: "/æmˈbɪɡjuəs/",
    meaning: "adj. 模棱两可的；含糊不清的；有歧义的",
    examples: [
      { en: "The instructions were ambiguous and difficult to follow.", zh: "这些说明模棱两可，很难照做。" },
      { en: "His reply to my question was somewhat ambiguous.", zh: "他对我问题的回答有点含糊不清。" },
      { en: "The legal phrasing is ambiguous.", zh: "法律措辞有歧义。" }
    ]
  },
  {
    id: 2,
    word: "Comprehensive",
    ipa: "/ˌkɒmprɪˈhensɪv/",
    meaning: "adj. 全面的；广泛的；综合的",
    examples: [
      { en: "We offer a comprehensive range of goods and services.", zh: "我们提供广泛全面的商品和服务。" },
      { en: "She has a comprehensive knowledge of the subject.", zh: "她对这个学科有渊博的知识。" }
    ]
  },
  {
    id: 3,
    word: "Fluctuate",
    ipa: "/ˈflʌktʃueɪt/",
    meaning: "v. 波动；涨落；起伏",
    examples: [
      { en: "Vegetable prices fluctuate according to the season.", zh: "蔬菜价格随季节波动。" },
      { en: "His mood seems to fluctuate from day to day.", zh: "他的情绪似乎每天都在起伏不定。" },
      { en: "The temperature fluctuated between 20 and 30 degrees.", zh: "气温在20到30度之间波动。" }
    ]
  },
  {
    id: 4,
    word: "Inevitable",
    ipa: "/ɪnˈevɪtəbl/",
    meaning: "adj. 不可避免的；必然发生的",
    examples: [
      { en: "It was inevitable that there would be job losses.", zh: "裁员是不可避免的。" },
      { en: "Technological changes are inevitable.", zh: "技术变革是必然的。" }
    ]
  },
  {
    id: 5,
    word: "Mitigate",
    ipa: "/ˈmɪtɪɡeɪt/",
    meaning: "v. 减轻；缓和",
    examples: [
      { en: "Action needs to be taken to mitigate the pollution effects.", zh: "需要采取行动来减轻污染的影响。" },
      { en: "It is unclear how to mitigate the problems of cost.", zh: "目前还不清楚如何缓解成本问题。" }
    ]
  },
  {
    id: 6,
    word: "Detrimental",
    ipa: "/ˌdetrɪˈmentl/",
    meaning: "adj. 有害的；不利的",
    examples: [
      { en: "Poor eating habits are detrimental to health.", zh: "不良的饮食习惯对健康有害。" },
      { en: "These chemicals have a detrimental effect on the environment.", zh: "这些化学物质对环境有不利影响。" }
    ]
  },
  {
    id: 7,
    word: "Subsequent",
    ipa: "/ˈsʌbsɪkwənt/",
    meaning: "adj. 随后的；后来的",
    examples: [
      { en: "The theory was developed subsequent to the earthquake of 1906.", zh: "该理论是在1906年地震后发展的。" },
      { en: "Subsequent events proved him wrong.", zh: "后来的事件证明他错了。" }
    ]
  },
  {
    id: 8,
    word: "Hypothesis",
    ipa: "/haɪˈpɒθəsɪs/",
    meaning: "n. 假说；假设；前提",
    examples: [
      { en: "Several hypotheses for global warming have been suggested.", zh: "关于全球变暖已经提出了几种假说。" },
      { en: "We need to test this hypothesis.", zh: "我们需要验证这个假设。" }
    ]
  }
];

export default function IELTSVocabApp() {
  const [words, setWords] = useState(INITIAL_VOCAB_DATA);
  const [currentIndex, setCurrentIndex] = useState(0);
  const [isFlipped, setIsFlipped] = useState(false);
  const [masteredIds, setMasteredIds] = useState([]);
  const [activeTab, setActiveTab] = useState('study'); // 'study' or 'mastered'
  
  // 手势状态
  const [touchStart, setTouchStart] = useState(null);
  const [touchEnd, setTouchEnd] = useState(null);

  // 过滤出当前学习列表（排除已掌握的）
  const studyList = words.filter(w => !masteredIds.includes(w.id));
  
  // 当前显示的单词对象
  const currentWord = activeTab === 'study' 
    ? (studyList.length > 0 ? studyList[currentIndex % studyList.length] : null)
    : (masteredIds.length > 0 ? words.find(w => w.id === masteredIds[currentIndex % masteredIds.length]) : null);

  // 重置索引当列表变化时
  useEffect(() => {
    setCurrentIndex(0);
    setIsFlipped(false);
  }, [activeTab, masteredIds.length]);

  const handleNext = () => {
    setIsFlipped(false);
    setTimeout(() => {
      const listLength = activeTab === 'study' ? studyList.length : masteredIds.length;
      if (listLength > 0) {
        setCurrentIndex((prev) => (prev + 1) % listLength);
      }
    }, 150);
  };

  const handlePrev = () => {
    setIsFlipped(false);
    setTimeout(() => {
      const listLength = activeTab === 'study' ? studyList.length : masteredIds.length;
      if (listLength > 0) {
        setCurrentIndex((prev) => (prev - 1 + listLength) % listLength);
      }
    }, 150);
  };

  const toggleMastered = (id) => {
    // 震动反馈
    if (navigator.vibrate) navigator.vibrate(50);

    if (masteredIds.includes(id)) {
      setMasteredIds(masteredIds.filter(mid => mid !== id));
    } else {
      setMasteredIds([...masteredIds, id]);
      if (activeTab === 'study') {
        setIsFlipped(false);
        if (currentIndex >= studyList.length - 1) {
            setCurrentIndex(0);
        }
      }
    }
  };

  const speakWord = (text, e) => {
    e.stopPropagation();
    const utterance = new SpeechSynthesisUtterance(text);
    utterance.lang = 'en-GB';
    window.speechSynthesis.speak(utterance);
  };

  const calculateProgress = () => {
    return Math.round((masteredIds.length / words.length) * 100);
  };

  // 手势处理
  const minSwipeDistance = 50;
  
  const onTouchStart = (e) => {
    setTouchEnd(null);
    setTouchStart(e.targetTouches[0].clientX);
  };

  const onTouchMove = (e) => setTouchEnd(e.targetTouches[0].clientX);

  const onTouchEnd = () => {
    if (!touchStart || !touchEnd) return;
    const distance = touchStart - touchEnd;
    const isLeftSwipe = distance > minSwipeDistance;
    const isRightSwipe = distance < -minSwipeDistance;
    
    if (isLeftSwipe) handleNext();
    if (isRightSwipe) handlePrev();
  };

  // 完成页
  if (!currentWord && activeTab === 'study') {
    return (
      <div className="h-[100dvh] bg-slate-50 flex flex-col items-center justify-center p-6 text-center">
        <div className="bg-white p-8 rounded-3xl shadow-xl w-full max-w-sm">
          <Award className="w-20 h-20 text-yellow-500 mx-auto mb-6" />
          <h2 className="text-3xl font-bold text-slate-800 mb-3">恭喜完成！</h2>
          <p className="text-slate-600 mb-8">当前列表的单词已全部掌握。</p>
          <button 
            onClick={() => setMasteredIds([])}
            className="flex items-center justify-center gap-2 w-full py-4 bg-blue-600 text-white font-bold rounded-2xl hover:bg-blue-700 active:scale-95 transition-all shadow-lg shadow-blue-200"
          >
            <RotateCcw size={20} />
            重置进度
          </button>
          <button 
            onClick={() => setActiveTab('mastered')}
            className="mt-6 text-blue-600 font-medium py-2 px-4"
          >
            复习已掌握词汇
          </button>
        </div>
      </div>
    );
  }

  return (
    // 使用 100dvh 确保移动端高度适配
    <div className="h-[100dvh] flex flex-col bg-slate-50 font-sans text-slate-800 overflow-hidden select-none">
      
      {/* 顶部导航与进度 */}
      <header className="bg-white shadow-sm flex-none z-10">
        <div className="px-5 py-3 flex justify-between items-center">
          <div className="flex items-center gap-2">
            <GraduationCap className="text-blue-600 w-6 h-6" />
            <h1 className="text-lg font-bold tracking-tight">雅思核心词汇</h1>
          </div>
          <div className="text-xs font-bold text-slate-400 bg-slate-100 px-2 py-1 rounded-md">
            {masteredIds.length} / {words.length}
          </div>
        </div>
        <div className="h-1 w-full bg-slate-100">
          <div 
            className="h-full bg-blue-600 transition-all duration-500 ease-out"
            style={{ width: `${calculateProgress()}%` }}
          ></div>
        </div>
        
        {/* 模式切换 Tab */}
        <div className="flex px-4 py-2 bg-white border-b border-slate-100">
           <button 
            onClick={() => setActiveTab('study')}
            className={`flex-1 py-2 text-sm font-bold rounded-l-lg border-y border-l transition-colors ${
              activeTab === 'study' 
                ? 'bg-blue-50 border-blue-200 text-blue-700 z-10' 
                : 'bg-white border-slate-200 text-slate-400'
            }`}
          >
            学习中
          </button>
          <button 
            onClick={() => setActiveTab('mastered')}
            className={`flex-1 py-2 text-sm font-bold rounded-r-lg border transition-colors ${
              activeTab === 'mastered' 
                ? 'bg-green-50 border-green-200 text-green-700 z-10' 
                : 'bg-white border-slate-200 text-slate-400'
            }`}
          >
            已掌握
          </button>
        </div>
      </header>

      {/* 卡片区域 - Flex 1 自动填充剩余空间 */}
      <main 
        className="flex-1 flex flex-col justify-center p-4 relative overflow-hidden"
        onTouchStart={onTouchStart}
        onTouchMove={onTouchMove}
        onTouchEnd={onTouchEnd}
      >
        {currentWord ? (
          <div className="perspective-1000 w-full max-w-md mx-auto h-full max-h-[600px] relative">
            <div 
              className={`relative w-full h-full transition-transform duration-500 transform-style-3d cursor-pointer ${isFlipped ? 'rotate-y-180' : ''}`}
              onClick={() => setIsFlipped(!isFlipped)}
              style={{ transformStyle: 'preserve-3d' }}
            >
              {/* 正面 */}
              <div 
                className="absolute inset-0 backface-hidden bg-white rounded-3xl shadow-[0_8px_30px_rgb(0,0,0,0.06)] border border-slate-100 flex flex-col items-center justify-center p-6 text-center"
                style={{ backfaceVisibility: 'hidden' }}
              >
                <div className="absolute top-6 right-6 text-slate-300">
                    <span className="text-xs font-bold border border-slate-200 px-2 py-1 rounded">点击翻转</span>
                </div>
                
                <div className="flex-1 flex flex-col items-center justify-center w-full">
                    {/* 根据单词长度动态调整字体大小 */}
                    <h2 className={`${currentWord.word.length > 10 ? 'text-4xl' : 'text-5xl'} font-bold text-slate-800 mb-6 tracking-tight break-words max-w-full`}>
                        {currentWord.word}
                    </h2>
                    
                    <button 
                        onClick={(e) => speakWord(currentWord.word, e)}
                        className="flex items-center gap-2 bg-blue-50 px-5 py-3 rounded-full hover:bg-blue-100 active:bg-blue-200 transition-colors"
                    >
                        <span className="font-mono text-slate-600 text-lg">{currentWord.ipa}</span>
                        <Volume2 size={20} className="text-blue-600" />
                    </button>
                </div>
                
                <div className="text-slate-400 text-sm mt-auto flex items-center gap-2 opacity-60">
                    <ChevronLeft size={16} /> 左右滑动切换 <ChevronRight size={16} />
                </div>
              </div>

              {/* 背面 */}
              <div 
                className="absolute inset-0 backface-hidden rotate-y-180 bg-slate-50 rounded-3xl shadow-[0_8px_30px_rgb(0,0,0,0.06)] border border-slate-200 flex flex-col overflow-hidden"
                style={{ backfaceVisibility: 'hidden', transform: 'rotateY(180deg)' }}
              >
                {/* 背面顶部固定区 */}
                <div className="bg-white px-6 py-5 border-b border-slate-100 flex justify-between items-center flex-none">
                    <h3 className="text-2xl font-bold text-slate-800">{currentWord.word}</h3>
                    <button onClick={(e) => speakWord(currentWord.word, e)} className="p-2 bg-slate-100 rounded-full active:bg-slate-200">
                        <Volume2 size={20} className="text-blue-600" />
                    </button>
                </div>

                {/* 背面滚动内容区 */}
                <div className="flex-1 overflow-y-auto p-6 scrollbar-hide">
                    <div className="mb-6">
                        <span className="inline-block text-[10px] font-bold text-white bg-slate-400 px-2 py-0.5 rounded mb-2">释义</span>
                        <p className="text-xl text-slate-800 font-medium leading-relaxed">{currentWord.meaning}</p>
                    </div>

                    <div>
                        <span className="inline-block text-[10px] font-bold text-white bg-blue-400 px-2 py-0.5 rounded mb-3">例句</span>
                        <div className="space-y-4">
                            {currentWord.examples.map((ex, idx) => (
                            <div key={idx} className="bg-white p-4 rounded-xl shadow-sm border border-slate-100">
                                <p className="text-slate-800 text-[15px] mb-2 leading-normal font-medium">{ex.en}</p>
                                <p className="text-slate-500 text-sm leading-normal">{ex.zh}</p>
                            </div>
                            ))}
                        </div>
                    </div>
                    {/* 底部留白防止遮挡 */}
                    <div className="h-4"></div>
                </div>
              </div>
            </div>
          </div>
        ) : (
          <div className="flex flex-col items-center justify-center text-slate-400 h-full">
            <BookOpen size={48} className="mb-4 opacity-20" />
            <p>暂无单词</p>
          </div>
        )}
      </main>

      {/* 底部操作Dock - 贴底设计 */}
      <div className="bg-white border-t border-slate-200 px-6 py-4 pb-safe flex items-center justify-between gap-4 flex-none">
        <button 
            onClick={handlePrev}
            disabled={!currentWord}
            className="p-4 rounded-2xl text-slate-400 hover:bg-slate-50 active:bg-slate-100 disabled:opacity-30 transition-all"
        >
            <ChevronLeft size={28} />
        </button>

        <button 
            onClick={() => {
              if (currentWord) toggleMastered(currentWord.id);
            }}
            disabled={!currentWord}
            className={`flex-1 py-4 rounded-2xl font-bold text-lg shadow-sm flex items-center justify-center gap-2 transition-all active:scale-95 ${
              currentWord && masteredIds.includes(currentWord.id)
              ? 'bg-green-100 text-green-700 ring-2 ring-green-200'
              : 'bg-blue-600 text-white shadow-blue-200 shadow-md'
            }`}
        >
            {currentWord && masteredIds.includes(currentWord.id) ? (
                <>
                    <CheckCircle size={24} /> 
                    <span>已掌握</span>
                </>
            ) : (
                <span>标记为掌握</span>
            )}
        </button>

        <button 
            onClick={handleNext}
            disabled={!currentWord}
            className="p-4 rounded-2xl text-slate-400 hover:bg-slate-50 active:bg-slate-100 disabled:opacity-30 transition-all"
        >
            <ChevronRight size={28} />
        </button>
      </div>

      <style>{`
        .perspective-1000 { perspective: 1000px; }
        .transform-style-3d { transform-style: preserve-3d; }
        .backface-hidden { backface-visibility: hidden; }
        .rotate-y-180 { transform: rotateY(180deg); }
        .pb-safe { padding-bottom: env(safe-area-inset-bottom, 20px); }
        /* 隐藏滚动条但保留滚动功能 */
        .scrollbar-hide::-webkit-scrollbar { display: none; }
        .scrollbar-hide { -ms-overflow-style: none; scrollbar-width: none; }
      `}</style>
    </div>
  );
}
