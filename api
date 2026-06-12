// api/contact.js
// Drop this file into your GitHub repo at:  api/contact.js
// Vercel automatically turns any file in /api into a serverless endpoint.

module.exports = async function handler(req, res) {
  // Allow requests from your own site only
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Access-Control-Allow-Methods', 'POST, OPTIONS');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type');
  if (req.method === 'OPTIONS') return res.status(200).end();

  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  const { name, email, service, message } = req.body;

  if (!name || !email || !message) {
    return res.status(400).json({ error: 'Name, email and message are required.' });
  }

  const webhook = process.env.SHEETS_WEBHOOK;

  if (!webhook) {
    console.error('SHEETS_WEBHOOK environment variable is not set.');
    return res.status(500).json({ error: 'Server not configured yet.' });
  }

  try {
    const response = await fetch(webhook, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        name,
        email,
        service: service || 'Not specified',
        message
      })
    });

    const data = await response.json();

    if (data.success) {
      return res.json({ success: true });
    } else {
      throw new Error('Apps Script returned failure');
    }
  } catch (err) {
    console.error('Google Sheets error:', err.message);
    return res.status(500).json({ error: 'Could not save submission. Try again.' });
  }
};
