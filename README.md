# 🌙 Sate Kambing Mobile App — Dark Aesthetic README

> **Mobile app sederhana namun elegan, dibuat dengan React Native + Expo.**
> Fokus pada estetika gelap, minimalis, dan konten kuliner sate kambing 😎🍢

---

## ✨ Fitur Utama
- 🥩 **Daftar Resep Sate Kambing** (kecap, Madura, pedas, dll.)
- ⭐ **Favoritkan resep** dengan penyimpanan lokal (AsyncStorage)
- 🧪 **Random Marinade Generator** untuk ide bumbu cepat
- 📝 **Tambah resep custom** langsung dari aplikasi
- 🔍 **Detail resep**: Ingredients + langkah memasak
- 🌙 **Tema gelap** (estetika modern minimalis)

---

## 📱 Screenshot (Dark Mode)
> Letakkan gambar preview pada `screenshot.png` (400–600px)

```
./screenshot.png
```

---

## 🚀 Cara Menjalankan
```bash
npm install -g expo-cli
expo init sate-kambing-app --template blank\ n# ganti App.js dengan file dari repo
expo install @react-native-async-storage/async-storage
expo start
```

---

## 📦 Struktur Repo
```
sate-kambing-mobile-app/
├── App.js
├── README.md
├── LICENSE
├── .gitignore
└── screenshot.png
```

---

## 🛠️ Teknologi
- React Native (Expo)
- AsyncStorage
- FlatList + Modal + ScrollView
- Aesthetic UI (dark theme)

---

## 🧵 Git Push
```bash
git init
git add .
git commit -m "Dark aesthetic README + mobile app"
git branch -M main
git remote add origin https://github.com/USERNAME/sate-kambing-app.git
git push -u origin main
```

---

/*
README: Sate Kambing - Mobile (React Native / Expo)

Repo purpose:
A small, single-file React Native (Expo) app showcasing:
- List of sate kambing variants
- Recipe details and steps
- Random marinade generator
- Ingredient checklist (toggle)
- Save favorites locally

How to run:
1. Install Expo CLI (if you don't have it):
   npm install -g expo-cli

2. Create project folder and place this file as `App.js` inside an Expo project:
   expo init sate-kambing-app (choose blank template)
   Replace generated App.js with this file.

3. Install optional dependency for persistent storage:
   expo install @react-native-async-storage/async-storage

4. Run the app:
   expo start

Notes:
- This is intentionally simple so you can push it to GitHub quickly.
- Feel free to expand with images, location lookup, or an ordering demo.
*/

// App.js
import React, { useState, useEffect } from 'react';
import { StyleSheet, Text, View, FlatList, TouchableOpacity, Button, Alert, Switch, TextInput, ScrollView } from 'react-native';
import AsyncStorage from '@react-native-async-storage/async-storage';

const SAMPLE_RECIPES = [
  {
    id: '1',
    name: 'Sate Kambing Bumbu Kecap',
    description: 'Sate kambing sederhana dengan kecap dan sambal.',
    ingredients: ['Daging kambing', 'Kecap manis', 'Bawang merah', 'Bawang putih', 'Cabe rawit'],
    steps: ['Tusuk daging', 'Bakar sambil dioles kecap', 'Sajikan dengan sambal']
  },
  {
    id: '2',
    name: 'Sate Kambing Madura-style',
    description: 'Bumbu kacang khas Madura.',
    ingredients: ['Daging kambing', 'Kacang tanah', 'Kecap', 'Jeruk limau'],
    steps: ['Sangrai kacang', 'Giling kacang + bumbu', 'Bakar sate + siram bumbu']
  },
  {
    id: '3',
    name: 'Sate Kambing Taliwang-inspired',
    description: 'Lebih pedas, aroma khas Lombok.',
    ingredients: ['Daging kambing', 'Cabe rawit', 'Bawang', 'Terasi'],
    steps: ['Haluskan bumbu', 'Marinasi daging', 'Bakar sambil dioleskan bumbu']
  }
];

export default function App() {
  const [recipes, setRecipes] = useState(SAMPLE_RECIPES);
  const [selected, setSelected] = useState(null);
  const [favorites, setFavorites] = useState({});
  const [showOnlyFav, setShowOnlyFav] = useState(false);
  const [customNote, setCustomNote] = useState('');

  useEffect(() => {
    (async () => {
      try {
        const raw = await AsyncStorage.getItem('@fav_sate');
        if (raw) setFavorites(JSON.parse(raw));
      } catch (e) {
        console.log('load fav error', e);
      }
    })();
  }, []);

  useEffect(() => {
    (async () => {
      try {
        await AsyncStorage.setItem('@fav_sate', JSON.stringify(favorites));
      } catch (e) {
        console.log('save fav error', e);
      }
    })();
  }, [favorites]);

  const toggleFav = (id) => {
    setFavorites(prev => ({ ...prev, [id]: !prev[id] }));
  };

  const generateMarinade = () => {
    const options = [
      'Kecap + Bawang merah + Jeruk nipis',
      'Kacang + Kecap + Cabe rawit',
      'Bawang putih + Ketumbar + Kunyit',
      'Sambal terasi + Jeruk limau',
      'Kecap + Sambal + Minyak wijen (fusion)'
    ];
    const pick = options[Math.floor(Math.random() * options.length)];
    Alert.alert('Marinade Recommendation', pick);
  };

  const addCustomRecipe = () => {
    if (!customNote.trim()) return Alert.alert('Isi nama resep singkat di bawah');
    const newId = Date.now().toString();
    const newR = { id: newId, name: customNote, description: 'User added', ingredients: ['Daging kambing'], steps: ['Siapkan', 'Bakar'] };
    setRecipes(prev => [newR, ...prev]);
    setCustomNote('');
  };

  const listData = showOnlyFav ? recipes.filter(r => favorites[r.id]) : recipes;

  return (
    <View style={styles.container}>
      <Text style={styles.title}>🍢 Sate Kambing - Mini App</Text>

      <View style={styles.row}>
        <Button title="Random Marinade" onPress={generateMarinade} />
        <View style={{width:12}} />
        <Button title="Add Quick Recipe" onPress={() => { setCustomNote('Resep baru saya'); }} />
      </View>

      <View style={styles.rowCenter}>
        <Text>Show only favorites</Text>
        <Switch value={showOnlyFav} onValueChange={setShowOnlyFav} />
      </View>

      <View style={styles.inputRow}>
        <TextInput style={styles.input} placeholder="Nama resep singkat..." value={customNote} onChangeText={setCustomNote} />
        <Button title="Add" onPress={addCustomRecipe} />
      </View>

      <FlatList
        data={listData}
        keyExtractor={item => item.id}
        style={{width:'100%'}}
        renderItem={({item}) => (
          <TouchableOpacity style={styles.card} onPress={() => setSelected(item)}>
            <View style={{flexDirection:'row', justifyContent:'space-between', alignItems:'center'}}>
              <Text style={styles.itemTitle}>{item.name}</Text>
              <TouchableOpacity onPress={() => toggleFav(item.id)}>
                <Text style={{fontSize:18}}>{favorites[item.id] ? '⭐' : '☆'}</Text>
              </TouchableOpacity>
            </View>
            <Text style={styles.small}>{item.description}</Text>
          </TouchableOpacity>
        )}
      />

      {selected && (
        <View style={styles.detail}>
          <ScrollView>
            <Text style={styles.detailTitle}>{selected.name}</Text>
            <Text style={styles.small}>{selected.description}</Text>

            <Text style={styles.sub}>Ingredients</Text>
            {selected.ingredients.map((ing, i) => (
              <Text key={i} style={styles.list}>• {ing}</Text>
            ))}

            <Text style={styles.sub}>Steps</Text>
            {selected.steps.map((s, i) => (
              <Text key={i} style={styles.list}>{i+1}. {s}</Text>
            ))}

            <View style={{height:12}} />
            <Button title="Close" onPress={() => setSelected(null)} />
          </ScrollView>
        </View>
      )}

    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#fff', alignItems: 'center', paddingTop: 50 },
  title: { fontSize: 22, fontWeight: '700', marginBottom: 12 },
  row: { flexDirection: 'row', marginBottom: 8 },
  rowCenter: { flexDirection: 'row', alignItems: 'center', gap:8, marginBottom: 8 },
  card: { width: '94%', backgroundColor: '#f8f8f8', padding: 10, marginVertical: 6, borderRadius: 8 },
  itemTitle: { fontSize: 16, fontWeight: '600' },
  small: { color: '#444', marginTop: 4 },
  detail: { position: 'absolute', bottom: 10, left: 10, right: 10, top: 120, backgroundColor: '#fff', borderRadius: 8, padding: 12, shadowColor: '#000', shadowOpacity: 0.1, elevation: 6 },
  detailTitle: { fontSize: 20, fontWeight: '700' },
  sub: { marginTop: 10, fontWeight: '700' },
  list: { marginLeft: 8, marginTop: 6 },
  inputRow: { flexDirection: 'row', width: '94%', alignItems: 'center', marginBottom: 8 },
  input: { flex: 1, borderWidth: 1, borderColor: '#ddd', padding: 8, marginRight: 8, borderRadius: 6 }
});

# LICENSE (MIT)
```
MIT License

Copyright (c) 2025 Anton Lepari & ChatGPT

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND.
```

# .gitignore
```
# Node
node_modules/
npm-debug.log

# Expo
.expo/
.expo-shared/

# React Native
*.log
*.tmp

# macOS
.DS_Store
```

# screenshot.png Placeholder
(Gunakan gambar sate kambing apa saja, misalnya 400x300px. Nama file: `screenshot.png`.)

# Git Commands untuk Push
```
git init
git add .
git commit -m "Initial commit: Sate Kambing Mobile App"
git branch -M main
git remote add origin https://github.com/USERNAME/sate-kambing-app.git
git push -u origin main
```
