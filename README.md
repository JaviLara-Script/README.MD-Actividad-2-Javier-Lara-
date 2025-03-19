/* Estructura de directorios:
- /backend -> Servidor Express.js con conexión a MongoDB
- /frontend -> Aplicación Next.js con Redux para gestión de estados
- README.md -> Instrucciones para ejecutar el proyecto
*/

// === BACKEND === //
// /backend/server.js
const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");
const app = express();
const Habit = require("./models/Habit");

app.use(cors());
app.use(express.json());

mongoose.connect("mongodb+srv://tu_usuario:tu_password@cluster.mongodb.net/habits", {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

app.get("/habits", async (req, res) => {
  const habits = await Habit.find();
  res.json(habits);
});

app.listen(5000, () => console.log("Backend corriendo en puerto 5000"));

// /backend/models/Habit.js
const mongoose = require("mongoose");
const HabitSchema = new mongoose.Schema({
  name: String,
  completedDays: Number,
});
module.exports = mongoose.model("Habit", HabitSchema);

// === FRONTEND === //
// /frontend/store.js
import { configureStore } from "@reduxjs/toolkit";
import habitsReducer from "./slices/habitsSlice";
export const store = configureStore({ reducer: { habits: habitsReducer } });

// /frontend/slices/habitsSlice.js
import { createSlice, createAsyncThunk } from "@reduxjs/toolkit";
export const fetchHabits = createAsyncThunk("habits/fetchHabits", async () => {
  const response = await fetch("http://localhost:5000/habits");
  return response.json();
});
const habitsSlice = createSlice({
  name: "habits",
  initialState: { list: [], status: "idle" },
  reducers: {},
  extraReducers: (builder) => {
    builder.addCase(fetchHabits.fulfilled, (state, action) => {
      state.list = action.payload;
      state.status = "succeeded";
    });
  },
});
export default habitsSlice.reducer;

// /frontend/pages/index.js
import { useEffect } from "react";
import { useDispatch, useSelector } from "react-redux";
import { fetchHabits } from "../slices/habitsSlice";
export default function Home() {
  const dispatch = useDispatch();
  const habits = useSelector((state) => state.habits.list);
  useEffect(() => { dispatch(fetchHabits()); }, [dispatch]);
  return <div>{habits.map((habit) => <p key={habit._id}>{habit.name}</p>)}</div>;
}

// === README.md === //
/*
# Gestión de Hábitos en Next.js y Express.js

## Instalación y ejecución

### Backend
1. Ve a la carpeta `backend` y ejecuta:
   ```bash
   npm install
   node server.js
   ```

### Frontend
1. Ve a la carpeta `frontend` y ejecuta:
   ```bash
   npm install
   npm run dev
   ```

### Notas
- MongoDB Atlas debe estar configurado.
- El servidor backend debe estar corriendo antes de ejecutar el frontend.
*/
