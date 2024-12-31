<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Enlightened Interiors</title>
  <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-50 text-gray-800">
  <!-- Header -->
  <header class="bg-white shadow-lg">
    <div class="container mx-auto flex justify-between items-center p-4">
      <h1 class="text-2xl font-bold text-indigo-600">Enlightened Interiors</h1>
      <nav class="space-x-6">
        <a href="#" class="text-gray-600 hover:text-indigo-600">Home</a>
        <a href="#" class="text-gray-600 hover:text-indigo-600">About</a>
        <a href="#" class="text-gray-600 hover:text-indigo-600">Services</a>
        <a href="#" class="text-gray-600 hover:text-indigo-600">Testimonials</a>
        <a href="#" class="text-gray-600 hover:text-indigo-600">Contact</a>
        <a href="#login" class="text-gray-600 hover:text-indigo-600">Login</a>
      </nav>
    </div>
  </header>

  <!-- Hero Section -->
  <section class="bg-cover bg-center h-screen flex items-center justify-center" style="background-image: url('https://source.unsplash.com/random/1600x900?cleaning,luxury');">
    <div class="text-center text-white bg-black bg-opacity-50 p-8 rounded">
      <h2 class="text-4xl font-extrabold">Enlightened Interiors</h2>
      <p class="text-lg mt-4">Experience unparalleled cleanliness and comfort.</p>
      <a href="#quote" class="mt-6 inline-block bg-indigo-600 text-white py-2 px-4 rounded hover:bg-indigo-700">Get a Quote</a>
    </div>
  </section>

  <!-- About Section -->
  <section id="about" class="py-12">
    <div class="container mx-auto text-center">
      <h3 class="text-3xl font-bold mb-4">About Us</h3>
      <p class="text-lg text-gray-700 max-w-2xl mx-auto">We are a premium cleaning service dedicated to providing luxury-level care to your spaces. From homes to offices, we deliver perfection in every detail.</p>
    </div>
  </section>

  <!-- Services Section -->
  <section id="services" class="bg-gray-100 py-12">
    <div class="container mx-auto">
      <h3 class="text-3xl font-bold text-center mb-8">Our Services</h3>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <div class="bg-white shadow-md p-6 rounded text-center">
          <h4 class="text-xl font-bold mb-2">Residential Cleaning</h4>
          <p>Detailed cleaning to ensure your home sparkles.</p>
        </div>
        <div class="bg-white shadow-md p-6 rounded text-center">
          <h4 class="text-xl font-bold mb-2">Office Cleaning</h4>
          <p>Maintain a professional and clean workspace.</p>
        </div>
        <div class="bg-white shadow-md p-6 rounded text-center">
          <h4 class="text-xl font-bold mb-2">Deep Cleaning</h4>
          <p>Thorough cleaning for a fresh and sanitized environment.</p>
        </div>
      </div>
    </div>
  </section>

  <!-- Quote Section -->
  <section id="quote" class="py-12">
    <div class="container mx-auto text-center">
      <h3 class="text-3xl font-bold mb-4">Get a Quote</h3>
      <p class="mb-8">Fill out the form below to receive a personalized quote for your cleaning needs.</p>
      <form class="max-w-lg mx-auto space-y-4">
        <input type="text" placeholder="Full Name" class="w-full border rounded px-4 py-2">
        <input type="email" placeholder="Email" class="w-full border rounded px-4 py-2">
        <textarea placeholder="Describe your cleaning needs" class="w-full border rounded px-4 py-2"></textarea>
        <button type="submit" class="w-full bg-indigo-600 text-white py-2 rounded hover:bg-indigo-700">Submit</button>
      </form>
    </div>
  </section>

  <!-- Login Section -->
  <section id="login" class="py-12 bg-gray-100">
    <div class="container mx-auto text-center">
      <h3 class="text-3xl font-bold mb-4">Customer Login</h3>
      <p class="mb-8">Access exclusive deals and your booking history.</p>
      <form class="max-w-lg mx-auto space-y-4">
        <input type="email" placeholder="Email" class="w-full border rounded px-4 py-2">
        <input type="password" placeholder="Password" class="w-full border rounded px-4 py-2">
        <button type="submit" class="w-full bg-indigo-600 text-white py-2 rounded hover:bg-indigo-700">Login</button>
      </form>
      <p class="mt-4">Don't have an account? <a href="#" class="text-indigo-600 hover:underline">Sign up here</a>.</p>
    </div>
  </section>

  <!-- Footer -->
  <footer class="bg-gray-800 text-white py-6">
    <div class="container mx-auto text-center">
      <p>&copy; 2024 Enlightened Interiors. All rights reserved.</p>
    </div>
  </footer>
</body>
</html>


const express = require('express');
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
const cors = require('cors');

const app = express();
const PORT = process.env.PORT || 5000;
const JWT_SECRET = 'your_jwt_secret_key';

// Middleware
app.use(cors());
app.use(express.json());

// MongoDB Connection
mongoose.connect('mongodb://localhost:27017/enlightened_interiors', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
}).then(() => console.log('MongoDB Connected')).catch(err => console.log(err));

// User Schema
const userSchema = new mongoose.Schema({
  name: String,
  email: { type: String, unique: true },
  password: String,
  bookings: [
    {
      date: Date,
      service: String,
      details: String,
    }
  ]
});
const User = mongoose.model('User', userSchema);

// Routes

// Signup
app.post('/api/signup', async (req, res) => {
  const { name, email, password } = req.body;
  try {
    const hashedPassword = await bcrypt.hash(password, 10);
    const newUser = new User({ name, email, password: hashedPassword });
    await newUser.save();
    res.status(201).json({ message: 'User created successfully!' });
  } catch (error) {
    res.status(400).json({ error: 'Email already in use.' });
  }
});

// Login
app.post('/api/login', async (req, res) => {
  const { email, password } = req.body;
  try {
    const user = await User.findOne({ email });
    if (!user) return res.status(400).json({ error: 'User not found.' });

    const isPasswordValid = await bcrypt.compare(password, user.password);
    if (!isPasswordValid) return res.status(400).json({ error: 'Invalid credentials.' });

    const token = jwt.sign({ userId: user._id }, JWT_SECRET, { expiresIn: '1h' });
    res.json({ token });
  } catch (error) {
    res.status(500).json({ error: 'Something went wrong.' });
  }
});

// Booking History
app.get('/api/bookings', async (req, res) => {
  const token = req.headers['authorization'];
  if (!token) return res.status(403).json({ error: 'No token provided.' });

  try {
    const { userId } = jwt.verify(token, JWT_SECRET);
    const user = await User.findById(userId);
    res.json({ bookings: user.bookings });
  } catch (error) {
    res.status(403).json({ error: 'Invalid token.' });
  }
});

app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
