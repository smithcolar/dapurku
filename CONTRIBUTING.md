# 🤝 Contributing Guidelines untuk DapurKu

Terima kasih telah ingin berkontribusi pada DapurKu! Panduan ini membantu Anda memahami bagaimana cara berkontribusi dengan efektif.

## 📋 Daftar Isi

- [Code of Conduct](#code-of-conduct)
- [Cara Memulai](#cara-memulai)
- [Development Setup](#development-setup)
- [Git Workflow](#git-workflow)
- [Coding Standards](#coding-standards)
- [Testing](#testing)
- [Pull Request Process](#pull-request-process)
- [Komunikasi](#komunikasi)

---

## 📜 Code of Conduct

Kami berkomitmen untuk menciptakan lingkungan yang ramah dan inklusif. Semua kontributor harus:

- ✅ Menghormati semua anggota tim
- ✅ Menerima kritik konstruktif
- ✅ Fokus pada apa yang terbaik untuk komunitas
- ✅ Menunjukkan empati terhadap anggota lain

Perilaku yang tidak dapat diterima:
- ❌ Komentar rasis, seksis, atau diskriminatif
- ❌ Bullying atau pelecehan
- ❌ Ancaman atau kekerasan

Lapor pelanggaran ke: smithcolar@github.com

---

## 🚀 Cara Memulai

### 1. Fork Repository

```bash
git clone https://github.com/smithcolar/dapurku.git
cd dapurku
```

### 2. Buat Branch Fitur

```bash
git checkout -b feature/your-feature-name
# atau untuk bug fix:
git checkout -b fix/bug-description
```

### 3. Commit Changes

```bash
git add .
git commit -m "type: description"
```

### 4. Push & Create PR

```bash
git push origin feature/your-feature-name
```

---

## 💻 Development Setup

### Backend Development

```bash
cd backend

# Install dependencies
npm install

# Setup environment
cp .env.example .env
# Edit .env dengan settings lokal Anda

# Setup database
npm run migrate

# Run development server
npm run dev

# Run tests
npm run test

# Run linting
npm run lint
```

### Mobile Development

```bash
cd mobile

# Install dependencies
npm install

# Setup environment
cp .env.example .env

# Start Expo
npx expo start
# Scan dengan Expo app atau pilih: a (Android) / i (iOS)
```

### Web Admin Development

```bash
cd web-admin

# Install dependencies
npm install

# Start dev server
npm start
```

---

## 📦 Git Workflow

### Branch Naming Convention

```
feature/add-seller-dashboard      # Fitur baru
fix/payment-calculation           # Bug fix
docs/api-documentation            # Documentation
test/unit-tests-auth              # Tests
refactor/reduce-db-queries        # Refactoring
chore/update-dependencies          # Maintenance
```

### Commit Message Format

```
type(scope): description

[optional body]

[optional footer]
```

**Types:**
- `feat`: Fitur baru
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style (formatting, semicolons, etc)
- `refactor`: Code refactoring tanpa feature/fix
- `test`: Adding tests
- `chore`: Maintenance tasks
- `perf`: Performance improvements

**Examples:**

```bash
git commit -m "feat(auth): add JWT token refresh mechanism"
git commit -m "fix(orders): resolve payment status not updating"
git commit -m "docs(api): add seller endpoints documentation"
git commit -m "test(database): add user model tests"
```

---

## 💾 Coding Standards

### Backend (Node.js/Express)

```javascript
// ✅ Good: Clear naming, proper structure
async function createSeller(req, res) {
  try {
    const { email, phone, shopName } = req.body;
    
    // Validate input
    if (!email || !phone || !shopName) {
      return res.status(400).json({ error: 'Missing required fields' });
    }

    // Create seller
    const seller = await Seller.create({
      email,
      phone,
      shopName,
      createdAt: new Date()
    });

    res.status(201).json({ success: true, data: seller });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
}

// ❌ Bad: Poor naming, no validation
function cs(req, res) {
  const x = req.body;
  const y = Seller.create(x);
  res.json(y);
}
```

### Frontend (React/React Native)

```javascript
// ✅ Good: Functional component with hooks
import { useState, useEffect } from 'react';

export function SellerDashboard() {
  const [sellers, setSellers] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetchSellers();
  }, []);

  async function fetchSellers() {
    setLoading(true);
    try {
      const response = await fetch('/api/sellers');
      const data = await response.json();
      setSellers(data);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  }

  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage message={error} />;

  return (
    <div>
      {sellers.map(seller => (
        <SellerCard key={seller.id} seller={seller} />
      ))}
    </div>
  );
}
```

### General Rules

- **Indentation**: 2 spaces
- **Line length**: Max 100 characters
- **Naming**: camelCase untuk variables/functions, PascalCase untuk classes/components
- **Comments**: Gunakan untuk logic yang kompleks, bukan untuk obvious code
- **Error handling**: Always implement try-catch atau .catch()

---

## 🧪 Testing

### Backend Testing

```bash
# Run semua tests
npm run test

# Run dengan coverage
npm run test:coverage

# Run test file tertentu
npm run test -- auth.test.js
```

### Test Examples

```javascript
// ✅ Good test structure
describe('Seller Model', () => {
  describe('createSeller', () => {
    it('should create a new seller with valid data', async () => {
      const sellerData = {
        email: 'test@example.com',
        phone: '08123456789',
        shopName: 'Dapur Siti'
      };

      const seller = await Seller.create(sellerData);

      expect(seller).toBeDefined();
      expect(seller.email).toBe(sellerData.email);
      expect(seller.id).toBeTruthy();
    });

    it('should throw error with invalid email', async () => {
      const invalidData = {
        email: 'invalid-email',
        phone: '08123456789',
        shopName: 'Dapur Siti'
      };

      await expect(Seller.create(invalidData)).rejects.toThrow();
    });
  });
});
```

### Minimum Coverage Requirements

- **Statements**: 70%
- **Branches**: 65%
- **Functions**: 70%
- **Lines**: 70%

---

## 📝 Pull Request Process

### Sebelum membuat PR:

1. **Update branch dari main**
   ```bash
   git fetch origin
   git rebase origin/main
   ```

2. **Run tests lokal**
   ```bash
   npm run test
   npm run lint
   ```

3. **Update documentation** (jika diperlukan)

### PR Template

```markdown
## Description
Penjelasan singkat tentang perubahan ini.

## Type of Change
- [ ] 🐛 Bug fix (perubahan yang memperbaiki issue)
- [ ] ✨ Feature (perubahan yang menambah functionality)
- [ ] 📚 Documentation
- [ ] ⚡ Performance improvement
- [ ] 🔒 Security improvement

## Related Issue
Fixes #(issue number)

## Testing
- [ ] Tested locally
- [ ] Added test cases
- [ ] Test coverage maintained

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-reviewed code
- [ ] Comments added for complex logic
- [ ] Documentation updated
- [ ] No breaking changes
- [ ] Tests pass locally

## Screenshots (jika applicable)
[Tambahkan screenshot atau demo]
```

### PR Review Process

1. **Automated checks**: Linting, tests, coverage
2. **Code review**: Minimal 1 approver
3. **Conflicts**: Resolve sebelum merge
4. **Squash commits**: Optional, untuk clean history

---

## 🗣️ Komunikasi

### Discord/Slack Channels

- `#development` - Daily standup & technical discussions
- `#questions` - Q&A
- `#announcements` - Updates & releases

### Response Times

- Critical bugs: 24 jam
- Feature requests: 48 jam
- Questions: 72 jam

### Etiquette

- ✅ Jaga diskusi tetap profesional dan konstruktif
- ✅ Gunakan thread untuk organized discussion
- ✅ Avoid pinging orang tanpa urgent reason
- ✅ Share resources dan documentasi ketika relevan

---

## 📊 Kontribusi Terbaik

### Type A: Bug Fixes
```
- Temukan bug
- Buat test yang fail
- Fix bug
- Test pass
- Submit PR
```

### Type B: Features
```
- Diskusikan di issues dulu
- Design & planning
- Implement dengan tests
- Documentation
- Submit PR
```

### Type C: Documentation
```
- Improve clarity
- Add examples
- Fix typos
- Update outdated info
- Submit PR
```

### Type D: Code Quality
```
- Refactoring
- Performance optimization
- Improve test coverage
- Linting improvements
- Submit PR
```

---

## 🏆 Kontributor Recognition

Kami mengapresiasi semua kontribusi! 

Top contributors akan:
- ✨ Disebutkan di README
- 🎖️ Mendapat contributor badge
- 🤝 Diundang ke contributor meetings
- 💰 Eligible untuk rewards program (future)

---

## 📚 Resources

- [Express.js Docs](https://expressjs.com/)
- [React Documentation](https://react.dev/)
- [React Native Docs](https://reactnative.dev/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Midtrans Integration Guide](https://docs.midtrans.com/)

---

## ❓ Pertanyaan?

- Buka issue dengan label `question`
- Join Discord community
- Email: smithcolar@github.com

---

**Happy Contributing!** 🎉

Last Updated: 27 Mei 2026
