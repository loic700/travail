const express = require('express');
const fs = require('fs');
const path = require('path');
const cors = require('cors');

const app = express();
const PORT = process.env.PORT || 3000;
const DATA_DIR = process.env.DATA_DIR || path.join(__dirname, 'data');
const SHIFTS_FILE = path.join(DATA_DIR, 'shifts.json');
const SETTINGS_FILE = path.join(DATA_DIR, 'settings.json');
const PAYSLIPS_FILE = path.join(DATA_DIR, 'payslips.json');

// S'assure que le dossier de données existe
if (!fs.existsSync(DATA_DIR)) {
  fs.mkdirSync(DATA_DIR, { recursive: true });
}

function readJsonFile(filePath, defaultValue) {
  try {
    if (!fs.existsSync(filePath)) return defaultValue;
    const raw = fs.readFileSync(filePath, 'utf-8');
    return raw ? JSON.parse(raw) : defaultValue;
  } catch (e) {
    console.error('Erreur de lecture', filePath, e);
    return defaultValue;
  }
}

function writeJsonFile(filePath, data) {
  fs.writeFileSync(filePath, JSON.stringify(data, null, 2), 'utf-8');
}

app.use(cors());
// Limite relevée : payslips.json peut contenir des PDF/images en base64
app.use(express.json({ limit: '15mb' }));

// --- Santé du serveur ---
app.get('/api/health', (req, res) => {
  res.json({ status: 'ok' });
});

// --- Shifts (planning des jours travaillés) ---
app.get('/api/shifts', (req, res) => {
  const data = readJsonFile(SHIFTS_FILE, {});
  res.json(data);
});

app.post('/api/shifts', (req, res) => {
  try {
    writeJsonFile(SHIFTS_FILE, req.body || {});
    res.json({ ok: true });
  } catch (e) {
    res.status(500).json({ ok: false, error: e.message });
  }
});

// --- Réglages (taux, seuils, primes...) ---
app.get('/api/settings', (req, res) => {
  const data = readJsonFile(SETTINGS_FILE, {});
  res.json(data);
});

app.post('/api/settings', (req, res) => {
  try {
    writeJsonFile(SETTINGS_FILE, req.body || {});
    res.json({ ok: true });
  } catch (e) {
    res.status(500).json({ ok: false, error: e.message });
  }
});

// --- Paies (historique des paies reçues, une entrée par mois travaillé) ---
app.get('/api/payslips', (req, res) => {
  const data = readJsonFile(PAYSLIPS_FILE, {});
  res.json(data);
});

app.post('/api/payslips', (req, res) => {
  try {
    writeJsonFile(PAYSLIPS_FILE, req.body || {});
    res.json({ ok: true });
  } catch (e) {
    res.status(500).json({ ok: false, error: e.message });
  }
});

app.listen(PORT, () => {
  console.log(`Serveur de sauvegarde des heures démarré sur le port ${PORT}`);
  console.log(`Données stockées dans : ${DATA_DIR}`);
});
