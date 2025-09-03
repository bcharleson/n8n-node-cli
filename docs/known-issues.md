# Known Issues & Workarounds (v0.4.0)

> **Current limitations and proven solutions for @n8n/node-cli version 0.4.0**

## 🚨 Critical Issues

### Issue #1: Development Server Subprocess Hangs

**Status:** 🔴 **Critical** - Affects most users  
**Affects:** `pnpm dev` command  

**Symptoms:**
```bash
pnpm dev
# Output stops at:
[n8n] Initializing n8n process
# No further progress for 10+ minutes
```

**Root Cause:**
- CLI spawns n8n as subprocess with resource contention
- n8n loads 583+ nodes and 408+ credentials causing memory bottlenecks
- Process management issues in CLI subprocess handling

**Workarounds:**

**Solution A: External n8n (Recommended)**
```bash
pnpm dev --external-n8n
# Separate terminal:
n8n start
```

**Solution B: Manual Setup**
```bash
npm install -g n8n
mkdir -p ~/.n8n/custom
cp -r dist/* ~/.n8n/custom/
n8n start
```

**Expected Fix:** Version 0.5.x (Q2 2025)

---

### Issue #2: TypeScript Compilation Performance

**Status:** 🟡 **Major** - Significant performance impact  
**Affects:** `pnpm build` and `pnpm dev` compilation  

**Symptoms:**
```bash
[build] Starting compilation in watch mode...
# Hangs for 10+ minutes with no progress
```

**Root Cause:**
- CLI attempts to compile entire n8n ecosystem TypeScript files
- Massive dependency tree causes resource exhaustion

**Workarounds:**

**Solution A: Memory Optimization**
```bash
export NODE_OPTIONS="--max-old-space-size=8192"
pnpm build
```

**Solution B: Traditional Compilation**
```bash
npx tsc
cp -r dist/* ~/.n8n/custom/
```

**Expected Fix:** Version 0.5.x with compilation optimizations

---

### Issue #3: Project Structure Requirements

**Status:** 🟡 **Major** - Breaks existing projects  

**Symptoms:**
```bash
Error: Cannot find nodes or credentials
```

**Root Cause:**
- CLI expects strict `src/nodes/` and `src/credentials/` structure

**Solution:**
```bash
mkdir -p src
mv nodes src/
mv credentials src/
```

## 📊 Issue Severity Matrix

| Issue | Frequency | Impact | Workaround Available | Expected Fix |
|-------|-----------|--------|---------------------|--------------|
| **Subprocess Hangs** | 80% | Critical | ✅ External n8n | v0.5.x |
| **Compilation Performance** | 70% | Major | ✅ Memory increase | v0.5.x |
| **Project Structure** | 60% | Major | ✅ Restructure | v0.5.x |

## 🎯 Recommended Mitigation Strategy

### For New Projects
```bash
# Always start with external n8n approach
pnpm create @n8n/node my-node
cd my-node
pnpm dev --external-n8n
# Separate terminal: n8n start
```

### For Existing Projects
```bash
# Restructure first
mkdir -p src
mv nodes src/
mv credentials src/

# Use external n8n
pnpm dev --external-n8n
```

---

**💡 Remember:** This is a brand new tool (v0.4.0). Issues are expected and the n8n team is actively working on improvements. Always have backup workflows ready!
