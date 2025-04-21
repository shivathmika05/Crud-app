const express = require('express');
const fs = require('fs');
const app = express();

app.use(express.json());

// File Path
const FILE_PATH = './data.json';

// Read Data from JSON
const readData = () => {
  const data = fs.readFileSync(FILE_PATH);
  return JSON.parse(data);
};

// Write Data to JSON
const writeData = (data) => {
  fs.writeFileSync(FILE_PATH, JSON.stringify(data, null, 2));
};

// ✅ Create - Add a New Record
app.post('/users', (req, res) => {
  const users = readData();
  const newUser = { id: Date.now(), ...req.body };
  users.push(newUser);
  writeData(users);
  res.status(201).json(newUser);
});

// ✅ Read - Get All Users
app.get('/users', (req, res) => {
  const users = readData();
  res.json(users);
});


// ✅ Read - Get User by ID
app.get('/users/:id', (req, res) => {
  const users = readData();
  const user = users.find(u => u.id == req.params.id);
  if (!user) return res.status(404).json({ error: "User not found" });
  res.json(user);
});

// ✅ Update - Edit User by ID
app.put('/users/:id', (req, res) => {
  const users = readData();
  const index = users.findIndex(u => u.id == req.params.id);
  if (index === -1) return res.status(404).json({ error: "User not found" });

  users[index] = { ...users[index], ...req.body };
  writeData(users);
  res.json(users[index]);
});

// ✅ Delete - Remove User by ID
app.delete('/users/:id', (req, res) => {
  let users = readData();
  const newUsers = users.filter(u => u.id != req.params.id);

  if (users.length === newUsers.length) {
    return res.status(404).json({ error: "User not found" });
  }

  writeData(newUsers);
  res.json({ message: "User deleted successfully" });
});

// Start the Server
app.listen(3000, () => {
  console.log('Server running on http://localhost:3000');
});
