# Test OpenAI Integration

## Check Backend Logs

1. **Uită-te la fereastra PowerShell unde rulează backend-ul**
2. **Caută erori** precum:
   - `Cannot find module 'openai'`
   - `Error: OpenAI API error`
   - TypeScript compilation errors

## Posibile Probleme:

### 1. Backend nu s-a recompilat
Backend-ul folosește TypeScript și trebuie recompilat când adaugi fișiere noi!

**Soluție**: Restart backend:
```powershell
# În fereastra backend, apasă Ctrl+C
# Apoi:
npm run dev
```

### 2. API Key invalid sau rate limit
OpenAI API key-ul poate avea probleme.

**Test manual în PowerShell**:
```powershell
curl https://api.openai.com/v1/chat/completions `
  -H "Content-Type: application/json" `
  -H "Authorization: Bearer YOUR_OPENAI_API_KEY_HERE" `
  -d '{\"model\": \"gpt-4o-mini\", \"messages\": [{\"role\": \"user\", \"content\": \"Hi\"}]}'
```

### 3. Backend încă folosește cache-ul vechi

**Soluție**: Șterge cache și rebuild:
```powershell
cd backend
Remove-Item -Recurse -Force dist
npm run dev
```

---

## Quick Fix - Restart Backend Complet

```powershell
# 1. Oprește backend (Ctrl+C în fereastra backend)

# 2. În terminalul tău:
cd C:\Users\ghile\Desktop\Haufe\review-local-ai\backend

# 3. Șterge dist folder (cache)
Remove-Item -Recurse -Force dist -ErrorAction SilentlyContinue

# 4. Pornește din nou
npm run dev
```

Ar trebui să vezi în logs:
```
Server running on port 3000
✓ MongoDB connected
✓ Using OpenAI API
```

---

## Test Manual OpenAI

Dacă vrei să testezi direct OpenAI fără backend:

**Node.js Quick Test**:
```javascript
// test-openai.js
import OpenAI from 'openai';

const openai = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY || 'YOUR_OPENAI_API_KEY_HERE'
});

const start = Date.now();
const completion = await openai.chat.completions.create({
  model: 'gpt-4o-mini',
  messages: [{ role: 'user', content: 'Say hi' }]
});
const duration = Date.now() - start;

console.log('Response:', completion.choices[0].message.content);
console.log('Time:', duration + 'ms');
```

Rulează:
```powershell
node test-openai.js
```

Ar trebui să dureze **1-3 secunde**!

---

## Debugging în Browser

Deschide **Developer Tools** (F12) → **Network** tab și vezi:
- Care endpoint se apelează? `/api/review/code`?
- Cât durează request-ul?
- Ce status code primești?

Dacă durează mult, problema e în backend!

