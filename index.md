# VetSathi (पशु साथी) — React & React Native Production Code Export

इस दस्तावेज़ में **VetSathi** के मुख्य मोबाइल-फर्स्ट प्रवाह (Farmer Map Home, 2-Step Emergency SOS, और Live Doctor Tracking) के पूर्ण, उत्पादन-स्तरीय (Production-Ready) **React / Next.js (Tailwind CSS)** एवं **React Native (Expo)** कंपोनेंट्स शामिल हैं।

---

## 1. React / Next.js (Tailwind CSS + Lucide Icons)

### `components/FarmerMapHome.tsx`
```tsx
import React, { useState } from 'react';
import { 
  MapPin, 
  Search, 
  Mic, 
  Stethoscope, 
  Calendar, 
  AlertTriangle, 
  ArrowRight,
  Menu,
  ChevronDown,
  PhoneCall,
  CheckCircle2,
  Navigation,
  Bot
} from 'lucide-react';

interface Animal {
  id: string;
  name: string;
  type: string;
  tag: string;
  age: string;
}

const REGISTERED_ANIMALS: Animal[] = [
  { id: '1', name: 'राधा', type: 'गाय (Gir Cow)', tag: '#8492', age: '4.5 वर्ष' },
  { id: '2', name: 'काली', type: 'भैंस (Murrah)', tag: '#3301', age: '3 वर्ष' },
  { id: '3', name: 'गौरी', type: 'गाय (Sahiwal)', tag: '#5521', age: '2 वर्ष' },
];

const EMERGENCY_SYMPTOMS = [
  'गंभीर चोट / खून',
  'सांस लेने में दिक्कत',
  'अचानक गिर गया',
  'प्रसव में समस्या',
  'तेज़ बुखार',
  'विषाक्तता का संदेह',
  'अन्य आपातकाल',
];

export default function VetSathiFarmerApp() {
  const [activeTab, setActiveTab] = useState<'home' | 'animals' | 'bookings' | 'profile'>('home');
  const [emergencyStep, setEmergencyStep] = useState<number>(0); // 0: Home, 1: Symptom, 2: Animal, 3: Confirm, 4: Tracking
  const [selectedSymptom, setSelectedSymptom] = useState<string>('सांस लेने में दिक्कत');
  const [selectedAnimal, setSelectedAnimal] = useState<string>('1');

  // Trigger Native Phone Call
  const handleCallDoctor = () => {
    window.location.href = 'tel:+919876543210';
  };

  return (
    <div className="flex justify-center items-center min-h-screen bg-slate-900 font-sans p-2">
      {/* 390px Mobile Frame */}
      <div className="relative w-full max-w-[390px] h-[844px] bg-[#f1fcf4] overflow-hidden flex flex-col shadow-2xl rounded-[40px] border-4 border-slate-800">
        
        {/* ================= STICKY HEADER ================= */}
        <header className="absolute top-0 left-0 right-0 z-30 pt-10 pb-3 px-4 bg-white/90 backdrop-blur-md border-b border-emerald-950/5 flex items-center justify-between shadow-sm">
          <button className="w-10 h-10 rounded-full bg-white shadow-sm flex items-center justify-center text-emerald-900 border border-slate-100">
            <Menu className="w-5 h-5" />
          </button>
          
          <div className="flex items-center gap-1.5 px-3 py-1.5 bg-emerald-50 rounded-full border border-emerald-200/50">
            <MapPin className="w-4 h-4 text-emerald-700" />
            <span className="text-xs font-bold text-emerald-950">नमस्ते, रामलाल जी • करनाल</span>
            <ChevronDown className="w-3.5 h-3.5 text-emerald-800" />
          </div>

          <div className="w-10 h-10 rounded-full bg-[#0d5c3a] text-white font-bold flex items-center justify-center text-sm shadow-sm ring-2 ring-emerald-100">
            र
          </div>
        </header>

        {/* ================= MAP CANVAS (80%+ Viewport) ================= */}
        <div className="absolute inset-0 z-0 bg-[#e6f3eb]">
          <svg className="w-full h-full object-cover opacity-80" xmlns="http://www.w3.org/2000/svg">
            <rect width="100%" height="100%" fill="#e8f3ec" />
            <path d="M -20 280 Q 150 260 220 400 T 420 520" fill="none" stroke="#ffffff" strokeWidth="18" />
            <path d="M 60 -20 Q 90 220 220 400 T 360 860" fill="none" stroke="#d5e8dc" strokeWidth="10" />
            <path d="M 220 400 L 290 470" fill="none" stroke="#10b981" strokeWidth="4" strokeDasharray="6 4" />
            
            {/* Farmer Location Radar Pin */}
            <circle cx="195" cy="380" r="32" fill="#0d5c3a" fillOpacity="0.15" />
            <circle cx="195" cy="380" r="10" fill="#0d5c3a" />
            <circle cx="195" cy="380" r="4" fill="#ffffff" />

            {/* Nearby Vet Doctor Pin */}
            <g transform="translate(270, 450)">
              <circle cx="16" cy="16" r="18" fill="#dc2626" className="drop-shadow-md" />
              <text x="16" y="21" fontSize="13" textAnchor="middle" fill="#ffffff">🐾</text>
            </g>
          </svg>

          {/* Ask VetSathi AI Floating Button */}
          <button 
            onClick={() => alert('VetSathi AI (Qwen/Llama) पशु परामर्श सहायक खुल रहा है...')}
            className="absolute top-28 right-4 z-20 flex items-center gap-1.5 px-3 py-2 bg-white/95 text-emerald-900 rounded-full shadow-lg border border-emerald-200 text-xs font-bold active:scale-95 transition"
          >
            <Bot className="w-4 h-4 text-emerald-700" />
            <span>AI साथी</span>
          </button>
        </div>

        {/* ================= STEP 0: MAIN HOME BOTTOM SHEET ================= */}
        {emergencyStep === 0 && (
          <div className="mt-auto relative z-20 bg-white rounded-t-[32px] shadow-[0_-10px_35px_rgba(0,0,0,0.1)] px-5 pt-3 pb-6 border-t border-emerald-950/5">
            <div className="w-10 h-1 bg-slate-200 rounded-full mx-auto mb-4" />
            
            {/* Search Bar */}
            <div className="flex items-center gap-2.5 bg-slate-50 border border-slate-200/90 rounded-2xl px-4 py-3 mb-4">
              <Search className="w-4 h-4 text-slate-400" />
              <input 
                type="text" 
                placeholder="डॉक्टर या सेवा खोजें..." 
                className="bg-transparent text-xs w-full outline-none font-medium text-slate-800"
              />
              <Mic className="w-4 h-4 text-emerald-700 cursor-pointer" />
            </div>

            {/* Primary Action 3 Cards */}
            <div className="grid grid-cols-3 gap-2.5 mb-4">
              <button className="flex flex-col items-center justify-center p-3 rounded-2xl bg-[#ebf6ee] border border-emerald-200/60 text-center active:scale-95 transition">
                <div className="w-10 h-10 rounded-xl bg-white shadow-sm flex items-center justify-center text-emerald-800 mb-1.5">
                  <Stethoscope className="w-5 h-5" />
                </div>
                <span className="text-[11px] font-bold text-emerald-950">पास के डॉक्टर</span>
              </button>

              <button className="flex flex-col items-center justify-center p-3 rounded-2xl bg-[#ebf6ee] border border-emerald-200/60 text-center active:scale-95 transition">
                <div className="w-10 h-10 rounded-xl bg-white shadow-sm flex items-center justify-center text-emerald-800 mb-1.5">
                  <Calendar className="w-5 h-5" />
                </div>
                <span className="text-[11px] font-bold text-emerald-950">विज़िट बुक करें</span>
              </button>

              <button className="flex flex-col items-center justify-center p-3 rounded-2xl bg-[#fff7ed] border border-orange-200/60 text-center active:scale-95 transition">
                <div className="w-10 h-10 rounded-xl bg-white shadow-sm flex items-center justify-center text-orange-800 mb-1.5 text-lg">
                  🐄
                </div>
                <span className="text-[11px] font-bold text-orange-950">मेरे पशु</span>
              </button>
            </div>

            {/* Red Fast Emergency SOS Banner */}
            <button 
              onClick={() => setEmergencyStep(1)}
              className="w-full flex items-center justify-between p-3.5 rounded-2xl bg-gradient-to-r from-red-600 to-rose-700 text-white shadow-lg shadow-red-600/30 active:scale-[0.98] transition"
            >
              <div className="flex items-center gap-3">
                <div className="w-9 h-9 rounded-full bg-white/20 flex items-center justify-center animate-pulse">
                  <AlertTriangle className="w-5 h-5 text-white" />
                </div>
                <div className="text-left">
                  <div className="text-[10px] font-bold uppercase tracking-wider text-rose-100">Emergency Vet • 15 Min</div>
                  <div className="text-xs font-black">पशु की हालत गंभीर है? अभी डॉक्टर बुलाएं</div>
                </div>
              </div>
              <ArrowRight className="w-5 h-5 text-white" />
            </button>
          </div>
        )}

        {/* ================= STEP 1: SELECT EMERGENCY REASON ================= */}
        {emergencyStep === 1 && (
          <div className="mt-auto relative z-20 bg-white rounded-t-[32px] shadow-2xl p-5 border-t border-slate-100 max-h-[580px] overflow-y-auto">
            <div className="flex justify-between items-center mb-3">
              <h3 className="text-sm font-black text-slate-900 flex items-center gap-1.5">
                <span className="text-red-600">🚨</span> क्या समस्या है?
              </h3>
              <button onClick={() => setEmergencyStep(0)} className="text-xs font-bold text-slate-400">रद्द करें</button>
            </div>
            
            <div className="space-y-2 mb-4">
              {EMERGENCY_SYMPTOMS.map((symptom) => (
                <button
                  key={symptom}
                  onClick={() => setSelectedSymptom(symptom)}
                  className={`w-full p-3 rounded-xl border text-left text-xs font-bold flex justify-between items-center transition ${
                    selectedSymptom === symptom
                      ? 'border-emerald-600 bg-emerald-50 text-emerald-950'
                      : 'border-slate-200 bg-white text-slate-700 hover:bg-slate-50'
                  }`}
                >
                  <span>{symptom}</span>
                  {selectedSymptom === symptom && <CheckCircle2 className="w-4 h-4 text-emerald-600" />}
                </button>
              ))}
            </div>

            <button 
              onClick={() => setEmergencyStep(2)}
              className="w-full py-3 bg-[#0d5c3a] text-white font-bold rounded-xl text-xs flex items-center justify-center gap-1.5 shadow-md active:scale-95 transition"
            >
              <span>आगे बढ़ें (पशु चुनें)</span>
              <ArrowRight className="w-4 h-4" />
            </button>
          </div>
        )}

        {/* ================= STEP 2: SELECT REGISTERED ANIMAL ================= */}
        {emergencyStep === 2 && (
          <div className="mt-auto relative z-20 bg-white rounded-t-[32px] shadow-2xl p-5 border-t border-slate-100">
            <div className="flex justify-between items-center mb-3">
              <h3 className="text-sm font-black text-slate-900">किस पशु के लिए मदद चाहिए?</h3>
              <button onClick={() => setEmergencyStep(1)} className="text-xs font-bold text-slate-400">पीछे</button>
            </div>

            <div className="space-y-2.5 mb-4">
              {REGISTERED_ANIMALS.map((animal) => (
                <div 
                  key={animal.id}
                  onClick={() => setSelectedAnimal(animal.id)}
                  className={`p-3 rounded-2xl border flex items-center justify-between cursor-pointer transition ${
                    selectedAnimal === animal.id 
                      ? 'border-emerald-600 bg-emerald-50/80 ring-1 ring-emerald-600' 
                      : 'border-slate-200 bg-white'
                  }`}
                >
                  <div className="flex items-center gap-3">
                    <div className="w-10 h-10 rounded-xl bg-amber-100 flex items-center justify-center text-xl">
                      🐄
                    </div>
                    <div>
                      <h4 className="text-xs font-bold text-slate-900">{animal.name}</h4>
                      <p className="text-[11px] text-slate-500">{animal.type} • {animal.tag}</p>
                    </div>
                  </div>
                  <div className={`w-5 h-5 rounded-full border flex items-center justify-center ${
                    selectedAnimal === animal.id ? 'bg-emerald-600 border-emerald-600 text-white' : 'border-slate-300'
                  }`}>
                    {selectedAnimal === animal.id && <CheckCircle2 className="w-3.5 h-3.5" />}
                  </div>
                </div>
              ))}
            </div>

            <button 
              onClick={() => setEmergencyStep(3)}
              className="w-full py-3 bg-[#0d5c3a] text-white font-bold rounded-xl text-xs flex items-center justify-center gap-1.5 shadow-md active:scale-95 transition"
            >
              <span>पुष्टि करें व डॉक्टर बुलाएं</span>
              <ArrowRight className="w-4 h-4" />
            </button>
          </div>
        )}

        {/* ================= STEP 3 & 4: CONFIRMATION & LIVE TRACKING ================= */}
        {emergencyStep >= 3 && (
          <div className="mt-auto relative z-20 bg-white rounded-t-[32px] shadow-2xl p-5 border-t border-slate-100">
            <div className="flex items-center justify-between pb-3 border-b border-slate-100 mb-3">
              <div>
                <span className="text-[10px] font-extrabold uppercase px-2 py-0.5 bg-emerald-100 text-emerald-800 rounded-full">
                  12 मिनट में पहुंचेंगे
                </span>
                <h3 className="text-sm font-black text-slate-900 mt-1">डॉ. अरविन्द शर्मा आ रहे हैं</h3>
              </div>
              <div className="text-right">
                <span className="text-xs font-bold text-slate-600">2.8 km दूर</span>
                <p className="text-[10px] text-emerald-700 font-bold">★ 4.9 (142)</p>
              </div>
            </div>

            {/* Direct Calling / Messaging buttons */}
            <div className="grid grid-cols-2 gap-2.5 mb-3">
              <button 
                onClick={handleCallDoctor}
                className="py-3 px-3 rounded-xl bg-[#0d5c3a] text-white font-bold text-xs flex items-center justify-center gap-1.5 shadow-md active:scale-95 transition"
              >
                <PhoneCall className="w-4 h-4" />
                <span>डॉक्टर को कॉल करें</span>
              </button>

              <button 
                onClick={() => setEmergencyStep(0)}
                className="py-3 px-3 rounded-xl bg-slate-100 text-slate-700 font-bold text-xs flex items-center justify-center gap-1.5 active:scale-95 transition"
              >
                <span>होम पर जाएं</span>
              </button>
            </div>

            {/* Status Steps */}
            <div className="flex items-center justify-between text-[10px] font-bold text-slate-400 pt-2">
              <span className="text-emerald-700">✓ स्वीकार</span>
              <span className="text-emerald-700 font-extrabold">● रास्ते में (12m)</span>
              <span>○ पहुंचे</span>
              <span>○ उपचार पूरा</span>
            </div>
          </div>
        )}

        {/* ================= BOTTOM TAB NAVIGATION ================= */}
        <nav className="relative z-30 bg-white border-t border-slate-100 px-6 py-2.5 flex justify-between items-center">
          <button 
            onClick={() => { setActiveTab('home'); setEmergencyStep(0); }}
            className={`flex flex-col items-center text-[10px] font-bold ${activeTab === 'home' ? 'text-emerald-800' : 'text-slate-400'}`}
          >
            <Navigation className="w-4 h-4 mb-0.5" />
            <span>होम</span>
          </button>
          <button 
            onClick={() => setActiveTab('animals')}
            className={`flex flex-col items-center text-[10px] font-bold ${activeTab === 'animals' ? 'text-emerald-800' : 'text-slate-400'}`}
          >
            <span>🐾</span>
            <span>मेरे पशु</span>
          </button>
          <button 
            onClick={() => setActiveTab('bookings')}
            className={`flex flex-col items-center text-[10px] font-bold ${activeTab === 'bookings' ? 'text-emerald-800' : 'text-slate-400'}`}
          >
            <span>📋</span>
            <span>बुकिंग</span>
          </button>
          <button 
            onClick={() => setActiveTab('profile')}
            className={`flex flex-col items-center text-[10px] font-bold ${activeTab === 'profile' ? 'text-emerald-800' : 'text-slate-400'}`}
          >
            <span>👤</span>
            <span>प्रोफाइल</span>
          </button>
        </nav>
      </div>
    </div>
  );
}
```

---

## 2. React Native (Expo / Native Modules)

### `screens/FarmerHomeScreen.native.tsx`
```tsx
import React, { useState } from 'react';
import {
  StyleSheet,
  View,
  Text,
  TouchableOpacity,
  TextInput,
  ScrollView,
  SafeAreaView,
  Linking,
  Dimensions,
  StatusBar,
  Alert
} from 'react-native';

const { width, height } = Dimensions.get('window');

export default function VetSathiFarmerMobile() {
  const [emergencyStep, setEmergencyStep] = useState(0); // 0: Home, 1: Step Reason, 2: Tracking

  const handleEmergencyCall = () => {
    Linking.openURL('tel:9876543210').catch(() => {
      Alert.alert('त्रुटि', 'फोन डायलर खोलने में असमर्थ');
    });
  };

  return (
    <SafeAreaView style={styles.safeArea}>
      <StatusBar barStyle="dark-content" backgroundColor="#ffffff" />
      
      {/* 1. STICKY TOP APP BAR */}
      <View style={styles.header}>
        <TouchableOpacity style={styles.iconCircle}>
          <Text style={styles.iconText}>☰</Text>
        </TouchableOpacity>
        
        <View style={styles.locationPill}>
          <Text style={styles.locationPin}>📍</Text>
          <Text style={styles.locationTitle}>नमस्ते, रामलाल जी • करनाल</Text>
        </View>

        <View style={styles.avatarCircle}>
          <Text style={styles.avatarText}>र</Text>
        </View>
      </View>

      {/* 2. MAP AREA (SIMULATED CANVAS / GOOGLE MAPS COMPATIBLE) */}
      <View style={styles.mapArea}>
        <View style={styles.farmerPinRadar}>
          <View style={styles.farmerPinCenter} />
        </View>

        <View style={styles.doctorMarker}>
          <Text style={{ fontSize: 16 }}>🐾</Text>
        </View>

        <TouchableOpacity 
          style={styles.aiButton}
          onPress={() => Alert.alert('VetSathi AI', 'ओपन-सोर्स AI पशु मित्र सक्रिय है')}
        >
          <Text style={styles.aiButtonText}>✦ AI साथी</Text>
        </TouchableOpacity>
      </View>

      {/* 3. HOME BOTTOM SHEET & WORKFLOW */}
      {emergencyStep === 0 ? (
        <View style={styles.bottomSheet}>
          <View style={styles.sheetHandle} />

          {/* Search Box */}
          <View style={styles.searchBox}>
            <Text style={{ marginRight: 6 }}>🔍</Text>
            <TextInput 
              placeholder="डॉक्टर या सेवा खोजें..." 
              placeholderTextColor="#94a3b8"
              style={styles.input}
            />
            <Text style={{ color: '#0d5c3a', fontWeight: 'bold' }}>🎙️</Text>
          </View>

          {/* 3 Quick Cards */}
          <View style={styles.quickGrid}>
            <TouchableOpacity style={styles.quickCard}>
              <Text style={styles.cardEmoji}>🩺</Text>
              <Text style={styles.cardText}>पास के डॉक्टर</Text>
            </TouchableOpacity>

            <TouchableOpacity style={styles.quickCard}>
              <Text style={styles.cardEmoji}>📅</Text>
              <Text style={styles.cardText}>विज़िट बुक करें</Text>
            </TouchableOpacity>

            <TouchableOpacity style={[styles.quickCard, { backgroundColor: '#fff7ed', borderColor: '#fed7aa' }]}>
              <Text style={styles.cardEmoji}>🐄</Text>
              <Text style={[styles.cardText, { color: '#7c2d12' }]}>मेरे पशु</Text>
            </TouchableOpacity>
          </View>

          {/* 🚨 Emergency SOS 1-Click Banner */}
          <TouchableOpacity 
            style={styles.sosButton}
            onPress={() => setEmergencyStep(1)}
            activeOpacity={0.9}
          >
            <View style={styles.sosLeft}>
              <View style={styles.sosBadge}>
                <Text style={{ fontSize: 16 }}>🚨</Text>
              </View>
              <View>
                <Text style={styles.sosSub}>EMERGENCY VET • 15 MIN</Text>
                <Text style={styles.sosMain}>पशु गंभीर है? अभी डॉक्टर बुलाएं</Text>
              </View>
            </View>
            <Text style={styles.sosArrow}>➔</Text>
          </TouchableOpacity>
        </View>
      ) : (
        /* 4. EMERGENCY ACTIVE TRACKING VIEW */
        <View style={styles.bottomSheet}>
          <View style={styles.sheetHandle} />
          
          <View style={styles.dispatchHeader}>
            <View>
              <Text style={styles.etaBadge}>12 मिनट में पहुंचेंगे</Text>
              <Text style={styles.doctorName}>डॉ. अरविन्द शर्मा आ रहे हैं</Text>
            </View>
            <Text style={styles.distanceText}>2.8 km दूर</Text>
          </View>

          <View style={styles.actionsRow}>
            <TouchableOpacity style={styles.callButton} onPress={handleEmergencyCall}>
              <Text style={styles.callButtonText}>📞 डॉक्टर को कॉल करें</Text>
            </TouchableOpacity>

            <TouchableOpacity style={styles.cancelButton} onPress={() => setEmergencyStep(0)}>
              <Text style={styles.cancelText}>होम पर लौटें</Text>
            </TouchableOpacity>
          </View>
        </View>
      )}

      {/* 4. BOTTOM TABS */}
      <View style={styles.navBar}>
        <TouchableOpacity style={styles.navItem}>
          <Text style={[styles.navText, { color: '#0d5c3a', fontWeight: 'bold' }]}>🧭 होम</Text>
        </TouchableOpacity>
        <TouchableOpacity style={styles.navItem}>
          <Text style={styles.navText}>🐾 मेरे पशु</Text>
        </TouchableOpacity>
        <TouchableOpacity style={styles.navItem}>
          <Text style={styles.navText}>📋 बुकिंग</Text>
        </TouchableOpacity>
        <TouchableOpacity style={styles.navItem}>
          <Text style={styles.navText}>👤 प्रोफाइल</Text>
        </TouchableOpacity>
      </View>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  safeArea: {
    flex: 1,
    backgroundColor: '#ffffff',
  },
  header: {
    position: 'absolute',
    top: 40,
    left: 16,
    right: 16,
    zIndex: 10,
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    backgroundColor: 'rgba(255, 255, 255, 0.95)',
    paddingVertical: 10,
    paddingHorizontal: 12,
    borderRadius: 24,
    borderWidth: 1,
    borderColor: '#e2e8f0',
    shadowColor: '#000',
    shadowOffset: { width: 0, height: 2 },
    shadowOpacity: 0.08,
    shadowRadius: 8,
    elevation: 3,
  },
  iconCircle: {
    width: 36,
    height: 36,
    borderRadius: 18,
    backgroundColor: '#f8fafc',
    justifyContent: 'center',
    alignItems: 'center',
  },
  iconText: {
    fontSize: 16,
    color: '#0f172a',
  },
  locationPill: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: '#ecfdf5',
    paddingHorizontal: 10,
    paddingVertical: 6,
    borderRadius: 16,
  },
  locationPin: {
    marginRight: 4,
    fontSize: 12,
  },
  locationTitle: {
    fontSize: 12,
    fontWeight: '700',
    color: '#064e3b',
  },
  avatarCircle: {
    width: 36,
    height: 36,
    borderRadius: 18,
    backgroundColor: '#0d5c3a',
    justifyContent: 'center',
    alignItems: 'center',
  },
  avatarText: {
    color: '#ffffff',
    fontWeight: 'bold',
    fontSize: 14,
  },
  mapArea: {
    flex: 1,
    backgroundColor: '#e6f3eb',
    justifyContent: 'center',
    alignItems: 'center',
  },
  farmerPinRadar: {
    width: 60,
    height: 60,
    borderRadius: 30,
    backgroundColor: 'rgba(13, 92, 58, 0.2)',
    justifyContent: 'center',
    alignItems: 'center',
  },
  farmerPinCenter: {
    width: 14,
    height: 14,
    borderRadius: 7,
    backgroundColor: '#0d5c3a',
    borderWidth: 2,
    borderColor: '#ffffff',
  },
  doctorMarker: {
    position: 'absolute',
    top: height * 0.45,
    right: 80,
    width: 36,
    height: 36,
    borderRadius: 18,
    backgroundColor: '#dc2626',
    justifyContent: 'center',
    alignItems: 'center',
    elevation: 4,
  },
  aiButton: {
    position: 'absolute',
    top: 110,
    right: 16,
    backgroundColor: '#ffffff',
    paddingVertical: 8,
    paddingHorizontal: 12,
    borderRadius: 20,
    borderWidth: 1,
    borderColor: '#a7f3d0',
    elevation: 3,
  },
  aiButtonText: {
    fontSize: 11,
    fontWeight: 'bold',
    color: '#065f46',
  },
  bottomSheet: {
    backgroundColor: '#ffffff',
    borderTopLeftRadius: 32,
    borderTopRightRadius: 32,
    paddingHorizontal: 16,
    paddingTop: 8,
    paddingBottom: 16,
    shadowColor: '#000',
    shadowOffset: { width: 0, height: -4 },
    shadowOpacity: 0.1,
    shadowRadius: 12,
    elevation: 10,
  },
  sheetHandle: {
    width: 40,
    height: 4,
    backgroundColor: '#e2e8f0',
    borderRadius: 2,
    alignSelf: 'center',
    marginBottom: 12,
  },
  searchBox: {
    flexDirection: 'row',
    alignItems: 'center',
    backgroundColor: '#f8fafc',
    borderWidth: 1,
    borderColor: '#e2e8f0',
    borderRadius: 16,
    paddingHorizontal: 12,
    paddingVertical: 10,
    marginBottom: 12,
  },
  input: {
    flex: 1,
    fontSize: 12,
    color: '#1e293b',
  },
  quickGrid: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    marginBottom: 12,
  },
  quickCard: {
    flex: 1,
    marginHorizontal: 3,
    backgroundColor: '#ebf6ee',
    borderWidth: 1,
    borderColor: '#d1fae5',
    borderRadius: 16,
    paddingVertical: 12,
    alignItems: 'center',
  },
  cardEmoji: {
    fontSize: 18,
    marginBottom: 4,
  },
  cardText: {
    fontSize: 11,
    fontWeight: '700',
    color: '#064e3b',
  },
  sosButton: {
    backgroundColor: '#dc2626',
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'space-between',
    padding: 14,
    borderRadius: 20,
  },
  sosLeft: {
    flexDirection: 'row',
    alignItems: 'center',
  },
  sosBadge: {
    width: 36,
    height: 36,
    borderRadius: 18,
    backgroundColor: 'rgba(255, 255, 255, 0.2)',
    justifyContent: 'center',
    alignItems: 'center',
    marginRight: 10,
  },
  sosSub: {
    color: '#fecdd3',
    fontSize: 9,
    fontWeight: '800',
  },
  sosMain: {
    color: '#ffffff',
    fontSize: 12,
    fontWeight: '900',
  },
  sosArrow: {
    color: '#ffffff',
    fontSize: 16,
    fontWeight: 'bold',
  },
  dispatchHeader: {
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    marginBottom: 16,
  },
  etaBadge: {
    color: '#065f46',
    backgroundColor: '#d1fae5',
    paddingHorizontal: 8,
    paddingVertical: 2,
    borderRadius: 10,
    fontSize: 10,
    fontWeight: '800',
    alignSelf: 'flex-start',
    marginBottom: 4,
  },
  doctorName: {
    fontSize: 13,
    fontWeight: '800',
    color: '#0f172a',
  },
  distanceText: {
    fontSize: 12,
    color: '#64748b',
    fontWeight: '700',
  },
  actionsRow: {
    flexDirection: 'row',
    gap: 8,
  },
  callButton: {
    flex: 2,
    backgroundColor: '#0d5c3a',
    paddingVertical: 12,
    borderRadius: 14,
    alignItems: 'center',
  },
  callButtonText: {
    color: '#ffffff',
    fontWeight: 'bold',
    fontSize: 12,
  },
  cancelButton: {
    flex: 1,
    backgroundColor: '#f1f5f9',
    paddingVertical: 12,
    borderRadius: 14,
    alignItems: 'center',
  },
  cancelText: {
    color: '#475569',
    fontWeight: '700',
    fontSize: 12,
  },
  navBar: {
    flexDirection: 'row',
    justifyContent: 'space-around',
    borderTopWidth: 1,
    borderTopColor: '#f1f5f9',
    paddingVertical: 10,
    backgroundColor: '#ffffff',
  },
  navItem: {
    alignItems: 'center',
  },
  navText: {
    fontSize: 11,
    color: '#94a3b8',
  },
});
```
