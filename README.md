# wjdxdbf16import React, { useState, useEffect } from 'react';
import { 
  User, 
  BookOpen, 
  Users, 
  FileText, 
  SearchCheck, 
  MessageCircle, 
  Send,
  X,
  AlertCircle,
  CheckCircle2,
  Sparkles,
  TrendingUp,
  Target,
  Plus,
  Trash2,
  ClipboardCheck,
  ShieldCheck,
  Loader2,
  Download,
  Eye,
  Settings,
  Languages,
  CheckCircle
} from 'lucide-react';

const App = () => {
  // --- 앱 단계 관리 ---
  const [appStep, setAppStep] = useState('auth'); 
  const [activeTab, setActiveTab] = useState('full-view');
  const [showChat, setShowChat] = useState(false);
  const [messages, setMessages] = useState([
    { id: 1, type: 'bot', text: '안녕하세요! 생기부 나이스 AI입니다. 입력하신 내용의 맞춤법과 표현을 실시간으로 점검해 드릴게요.' }
  ]);
  const [inputText, setInputText] = useState('');

  // --- 데이터 상태 ---
  const [studentData, setStudentData] = useState({
    profile: {
      name: '',
      residentNumber: '',
      school: '',
      grade: '1',
      class: '1',
      number: '',
      address: '',
    },
    attendance: {
      days: 190,
      illness: { absent: 0, late: 0, early: 0, out: 0 },
      unrecognized: { absent: 0, late: 0, early: 0, out: 0 },
      etc: { absent: 0, late: 0, early: 0, out: 0 },
      note: '개근'
    },
    subjects: [
      { id: 1, name: '수학 I', content: '함수의 극한 개념을 명확히 이해하고 있으며, 실생활 문제에 적용하는 능력이 탁월함' },
      { id: 2, name: '국어', content: '문학 작품의 시대적 배경을 분석하여 현대적 관점에서 재해석하는 능력이 우수함' }
    ],
    behavior: '항상 밝은 모습으로 급우들과 소통하며, 공동체 의식이 투철함'
  });

  // --- 실시간 맞춤법/표현 진단 로직 ---
  const checkSpelling = (text) => {
    if (!text) return [];
    const feedback = [];
    
    // 1. 마침표 검사
    if (text.length > 0 && !text.endsWith('.')) {
      feedback.push({ type: 'error', message: '문장 끝에 마침표(.)가 누락되었습니다.', suggestion: text + '.' });
    }
    
    // 2. 자주 틀리는 맞춤법 (예시)
    if (text.includes('탁월함')) {
      feedback.push({ type: 'info', message: "'탁월함' 보다는 구체적인 사례를 덧붙이는 것이 학종 평가에 유리합니다.", tip: '활동 사례 추가 권장' });
    }
    if (text.includes('열심히함')) {
      feedback.push({ type: 'error', message: "'열심히 함'으로 띄어 써야 합니다.", suggestion: '열심히 함' });
    }
    
    // 3. 금지어/부적절 표현
    if (text.includes('매우') || text.includes('너무')) {
      feedback.push({ type: 'warning', message: "주관적인 부사('매우', '너무') 사용을 줄이고 객관적 팩트 위주로 작성하세요." });
    }

    // 4. 글자수 진단
    const bytes = getByteLength(text);
    if (bytes < 100) {
      feedback.push({ type: 'warning', message: "내용이 너무 짧습니다. (현재 " + bytes + "바이트 / 권장 500바이트 이상)" });
    }

    return feedback;
  };

  const handleStartQuery = (e) => {
    e.preventDefault();
    if (!studentData.profile.name || !studentData.profile.school) {
      alert("성명과 학교명을 입력해주세요.");
      return;
    }
    setAppStep('loading');
    setTimeout(() => setAppStep('main'), 2000);
  };

  const handleProfileChange = (field, value) => {
    setStudentData(prev => ({ ...prev, profile: { ...prev.profile, [field]: value } }));
  };

  const handleSubjectChange = (id, value) => {
    setStudentData(prev => ({
      ...prev,
      subjects: prev.subjects.map(s => s.id === id ? { ...s, content: value } : s)
    }));
  };

  const getByteLength = (str) => {
    if (!str) return 0;
    let byte = 0;
    for (let i = 0; i < str.length; i++) {
      (str.charCodeAt(i) > 127) ? byte += 3 : byte += 1;
    }
    return byte;
  };

  if (appStep === 'auth') {
    return (
      <div className="h-screen w-full bg-slate-900 flex items-center justify-center p-4">
        <div className="bg-white w-full max-w-md rounded-[2.5rem] shadow-2xl p-10 animate-in zoom-in-95 duration-500">
          <div className="flex flex-col items-center mb-8">
            <div className="w-16 h-16 bg-blue-600 rounded-2xl flex items-center justify-center shadow-lg shadow-blue-200 mb-4 text-white">
              <ShieldCheck size={36} />
            </div>
            <h1 className="text-2xl font-black text-slate-800">생기부 나이스</h1>
            <p className="text-slate-400 text-sm mt-1 text-center">학생부 기록 및 맞춤법 통합 관리 시스템</p>
          </div>
          <form onSubmit={handleStartQuery} className="space-y-4">
            <input required className="w-full p-4 bg-slate-50 border border-slate-100 rounded-2xl outline-none" placeholder="성명" value={studentData.profile.name} onChange={(e) => handleProfileChange('name', e.target.value)} />
            <input required className="w-full p-4 bg-slate-50 border border-slate-100 rounded-2xl outline-none" placeholder="학교명" value={studentData.profile.school} onChange={(e) => handleProfileChange('school', e.target.value)} />
            <button type="submit" className="w-full bg-blue-600 text-white font-bold py-5 rounded-2xl shadow-xl hover:bg-blue-700 transition-all">접속하기</button>
          </form>
        </div>
      </div>
    );
  }

  if (appStep === 'loading') {
    return (
      <div className="h-screen w-full bg-white flex flex-col items-center justify-center p-4">
        <Loader2 size={48} className="text-blue-600 animate-spin mb-4" />
        <h2 className="text-xl font-bold text-slate-800">생활기록부 데이터 동기화 중...</h2>
      </div>
    );
  }

  return (
    <div className="flex h-screen bg-slate-200 font-sans text-slate-900 overflow-hidden">
      
      {/* 왼쪽 사이드바 (메뉴) */}
      <div className="w-64 bg-slate-900 text-white p-6 flex flex-col gap-2 z-20 shadow-2xl">
        <div className="mb-8 p-4 bg-white/5 rounded-2xl border border-white/10">
          <div className="text-sm font-bold text-blue-400">{studentData.profile.name}</div>
          <div className="text-[10px] text-slate-400">{studentData.profile.school}</div>
        </div>
        
        <nav className="space-y-1">
          <button onClick={() => setActiveTab('full-view')} className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl text-sm font-bold ${activeTab === 'full-view' ? 'bg-blue-600' : 'hover:bg-white/5'}`}><Eye size={16}/> 전체 열람</button>
          <button onClick={() => setActiveTab('subjects')} className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl text-sm font-bold ${activeTab === 'subjects' ? 'bg-blue-600' : 'hover:bg-white/5'}`}><BookOpen size={16}/> 교과 세특</button>
          <button onClick={() => setActiveTab('behavior')} className={`w-full flex items-center gap-3 px-4 py-3 rounded-xl text-sm font-bold ${activeTab === 'behavior' ? 'bg-blue-600' : 'hover:bg-white/5'}`}><Users size={16}/> 행동 특성</button>
        </nav>

        <div className="mt-auto p-4 bg-blue-900/30 rounded-2xl border border-blue-500/20">
          <div className="flex items-center gap-2 mb-2 text-blue-400">
            <Languages size={16} />
            <span className="text-[10px] font-black uppercase">Spell Checker</span>
          </div>
          <p className="text-[11px] text-slate-300 leading-tight">입력과 동시에 맞춤법과 표현을 AI가 검사합니다.</p>
        </div>
      </div>

      {/* 중앙 컨텐츠 (생기부 원본 스타일) */}
      <div className="flex-1 overflow-y-auto p-12 bg-slate-300 flex justify-center gap-8">
        
        {/* 한글 문서 스타일 컨테이너 */}
        <div className="w-[800px] min-h-[1100px] bg-white shadow-2xl p-[70px] relative">
          <h2 className="text-center text-3xl font-black underline underline-offset-8 mb-16 tracking-widest">학 교 생 활 기 록 부</h2>

          <section className="mb-10">
            <h3 className="text-sm font-bold mb-2">1. 인적·학적사항</h3>
            <table className="w-full border-collapse border border-black text-xs">
              <tbody>
                <tr>
                  <td className="border border-black bg-slate-50 p-2 font-bold w-24 text-center">성명</td>
                  <td className="border border-black p-2">{studentData.profile.name}</td>
                  <td className="border border-black bg-slate-50 p-2 font-bold w-24 text-center">학교명</td>
                  <td className="border border-black p-2">{studentData.profile.school}</td>
                </tr>
              </tbody>
            </table>
          </section>

          {(activeTab === 'full-view' || activeTab === 'subjects') && (
            <section className="mb-10">
              <h3 className="text-sm font-bold mb-2">2. 교과 세부능력 및 특기사항</h3>
              <table className="w-full border-collapse border border-black text-xs">
                <tbody>
                  {studentData.subjects.map(subject => (
                    <tr key={subject.id}>
                      <td className="border border-black bg-slate-50 p-3 font-bold w-24 text-center align-middle">{subject.name}</td>
                      <td className="border border-black p-3">
                        <textarea 
                          className="w-full min-h-[100px] bg-transparent outline-none resize-none leading-relaxed focus:bg-blue-50/30 transition-colors rounded"
                          value={subject.content}
                          onChange={(e) => handleSubjectChange(subject.id, e.target.value)}
                        />
                      </td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </section>
          )}

          {(activeTab === 'full-view' || activeTab === 'behavior') && (
            <section className="mb-10">
              <h3 className="text-sm font-bold mb-2">3. 행동특성 및 종합의견</h3>
              <div className="border border-black p-4">
                <textarea 
                  className="w-full min-h-[200px] bg-transparent outline-none resize-none leading-relaxed text-xs focus:bg-blue-50/30 transition-colors rounded"
                  value={studentData.behavior}
                  onChange={(e) => setStudentData({...studentData, behavior: e.target.value})}
                />
              </div>
            </section>
          )}
        </div>

        {/* 오른쪽 패널: 실시간 맞춤법 검사기 */}
        <div className="w-80 flex flex-col gap-4 animate-in slide-in-from-right-4 duration-500">
          <div className="bg-white rounded-3xl shadow-xl border border-slate-100 p-6 flex flex-col h-fit sticky top-0">
            <div className="flex items-center justify-between mb-6">
              <div className="flex items-center gap-2">
                <SearchCheck className="text-blue-600" size={20} />
                <h4 className="font-bold text-slate-800">실시간 진단</h4>
              </div>
              <span className="text-[10px] bg-blue-100 text-blue-700 px-2 py-1 rounded-full font-black uppercase">Live</span>
            </div>

            <div className="space-y-4 overflow-y-auto max-h-[600px] pr-2 custom-scrollbar">
              {/* 모든 입력 필드에 대한 종합 피드백 표시 */}
              {[...studentData.subjects.map(s => ({name: s.name, content: s.content})), {name: '행동특성', content: studentData.behavior}].map((item, idx) => {
                const errors = checkSpelling(item.content);
                if (errors.length === 0) return null;
                
                return (
                  <div key={idx} className="space-y-2 border-b border-slate-100 pb-4 last:border-0">
                    <div className="text-[10px] font-black text-slate-400 uppercase tracking-widest">{item.name}</div>
                    {errors.map((err, i) => (
                      <div key={i} className={`p-3 rounded-2xl text-xs flex gap-2 ${
                        err.type === 'error' ? 'bg-red-50 text-red-700 border border-red-100' :
                        err.type === 'warning' ? 'bg-amber-50 text-amber-700 border border-amber-100' :
                        'bg-blue-50 text-blue-700 border border-blue-100'
                      }`}>
                        <div className="mt-0.5">
                          {err.type === 'error' ? <AlertCircle size={14}/> : <Sparkles size={14}/>}
                        </div>
                        <div>
                          <p className="font-bold mb-1">{err.message}</p>
                          {err.suggestion && (
                            <div className="bg-white/50 p-2 rounded-lg mt-1 border border-current opacity-80">
                              <span className="text-[10px] font-bold block mb-1">수정 제안:</span>
                              <span className="italic">"{err.suggestion}"</span>
                            </div>
                          )}
                          {err.tip && <p className="mt-1 text-[10px] opacity-70 font-medium">💡 {err.tip}</p>}
                        </div>
                      </div>
                    ))}
                  </div>
                );
              })}

              {checkSpelling(studentData.behavior).length === 0 && studentData.subjects.every(s => checkSpelling(s.content).length === 0) && (
                <div className="py-12 flex flex-col items-center justify-center text-center">
                  <div className="w-12 h-12 bg-green-100 text-green-600 rounded-full flex items-center justify-center mb-4">
                    <CheckCircle size={24} />
                  </div>
                  <p className="text-sm font-bold text-slate-800">완벽합니다!</p>
                  <p className="text-xs text-slate-400 mt-1">현재 감지된 맞춤법 오류나<br/>개선 권장 사항이 없습니다.</p>
                </div>
              )}
            </div>
          </div>

          {/* AI 분석 요약 카드 */}
          <div className="bg-slate-900 rounded-3xl shadow-xl p-6 text-white">
            <div className="flex items-center gap-2 mb-4">
              <TrendingUp size={18} className="text-blue-400" />
              <h4 className="font-bold text-sm">기록 경쟁력 분석</h4>
            </div>
            <div className="space-y-3">
              <div className="flex justify-between text-[11px]">
                <span className="text-slate-400">구체성</span>
                <span className="font-bold">85%</span>
              </div>
              <div className="w-full h-1 bg-white/10 rounded-full overflow-hidden">
                <div className="h-full bg-blue-500 w-[85%]"></div>
              </div>
              <div className="flex justify-between text-[11px]">
                <span className="text-slate-400">전공 적합성</span>
                <span className="font-bold">70%</span>
              </div>
              <div className="w-full h-1 bg-white/10 rounded-full overflow-hidden">
                <div className="h-full bg-indigo-500 w-[70%]"></div>
              </div>
            </div>
          </div>
        </div>
      </div>

      {/* AI 메신저 기능 유지 */}
      <div className={`fixed bottom-8 right-8 transition-all duration-500 z-50 ${showChat ? 'scale-100 translate-y-0 opacity-100' : 'scale-0 translate-y-10 opacity-0 pointer-events-none'}`}>
        <div className="w-[380px] h-[550px] bg-white rounded-[2.5rem] shadow-2xl flex flex-col border border-slate-100 overflow-hidden">
          <div className="p-6 bg-blue-600 text-white flex items-center justify-between">
            <div className="flex items-center gap-3">
              <div className="w-10 h-10 bg-white/20 rounded-full flex items-center justify-center">
                <Sparkles size={20} />
              </div>
              <div>
                <h4 className="font-bold text-sm">생기부 AI 컨설턴트</h4>
                <p className="text-[10px] text-blue-200">데이터 기반 분석 활성화</p>
              </div>
            </div>
            <button onClick={() => setShowChat(false)} className="hover:bg-white/10 p-2 rounded-full transition-colors"><X size={20} /></button>
          </div>
          <div className="flex-1 overflow-y-auto p-6 space-y-4 bg-slate-50">
            {messages.map(msg => (
              <div key={msg.id} className={`flex ${msg.type === 'user' ? 'justify-end' : 'justify-start'}`}>
                <div className={`max-w-[85%] p-4 rounded-2xl text-xs font-medium ${msg.type === 'user' ? 'bg-blue-600 text-white rounded-br-none' : 'bg-white text-slate-800 rounded-bl-none border border-slate-100'}`}>
                  {msg.text}
                </div>
              </div>
            ))}
          </div>
          <div className="p-4 bg-white border-t flex gap-2">
            <input className="flex-1 bg-slate-100 rounded-xl px-4 py-2 text-xs outline-none" placeholder="질문하세요..." value={inputText} onChange={(e)=>setInputText(e.target.value)} onKeyPress={(e)=>e.key==='Enter' && (setMessages([...messages, {id:Date.now(), type:'user', text:inputText}]), setInputText(''))}/>
            <button className="bg-blue-600 text-white p-2 rounded-xl"><Send size={16}/></button>
          </div>
        </div>
      </div>

      {!showChat && (
        <button onClick={() => setShowChat(true)} className="fixed bottom-8 right-8 w-14 h-14 bg-blue-600 text-white rounded-full shadow-2xl flex items-center justify-center hover:scale-110 transition-all z-40">
          <MessageCircle size={24} fill="white" />
        </button>
      )}

      <style>{`
        .custom-scrollbar::-webkit-scrollbar { width: 4px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #e2e8f0; border-radius: 10px; }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover { background: #cbd5e1; }
      `}</style>
    </div>
  );
};

export default App;
