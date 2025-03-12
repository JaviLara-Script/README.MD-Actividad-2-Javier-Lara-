/* Proyecto de gestión de hábitos con Next.js y Redux */

// 1. Configuración inicial en Next.js
// Se inicia el proyecto con create-next-app
yarn create next-app frontend

// 2. Integración de Redux en Next.js
// Instalar dependencias
cd frontend
yarn add @reduxjs/toolkit react-redux

// Configurar Redux Store
// frontend/store/store.js
import { configureStore } from '@reduxjs/toolkit';
import habitsReducer from './slices/habitsSlice';

export const store = configureStore({
  reducer: {
    habits: habitsReducer,
  },
});

// frontend/slices/habitsSlice.js
import { createSlice } from '@reduxjs/toolkit';

const habitsSlice = createSlice({
  name: 'habits',
  initialState: [],
  reducers: {
    setHabits: (state, action) => action.payload,
  },
});

export const { setHabits } = habitsSlice.actions;
export default habitsSlice.reducer;

// Configurar Provider en frontend/pages/_app.js
import { Provider } from 'react-redux';
import { store } from '../store/store';

function MyApp({ Component, pageProps }) {
  return (
    <Provider store={store}>
      <Component {...pageProps} />
    </Provider>
  );
}
export default MyApp;

// 3. Integración request GET con backend para obtener hábitos
// frontend/pages/index.js
import { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { setHabits } from '../store/slices/habitsSlice';

export default function Home() {
  const dispatch = useDispatch();
  const habits = useSelector((state) => state.habits);

  useEffect(() => {
    fetch('http://localhost:5000/api/habits')
      .then((response) => response.json())
      .then((data) => dispatch(setHabits(data)));
  }, [dispatch]);

  return (
    <div>
      <h1>Lista de Hábitos</h1>
      <ul>
        {habits.map((habit) => (
          <li key={habit._id}>{habit.name}</li>
        ))}
      </ul>
    </div>
  );
}
