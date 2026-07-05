# Mobile Development Skill

## Purpose
Expert cross-platform mobile development with React Native and Flutter. Covers iOS and Android development, native modules, and mobile-specific patterns.

## React Native

### Component Structure
```tsx
import React, { useState, useEffect } from 'react';
import { View, Text, StyleSheet, FlatList, TouchableOpacity } from 'react-native';

interface User {
  id: string;
  name: string;
  email: string;
}

export const UserList: React.FC = () => {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUsers();
  }, []);

  const fetchUsers = async () => {
    try {
      const response = await fetch('https://api.example.com/users');
      const data = await response.json();
      setUsers(data);
    } finally {
      setLoading(false);
    }
  };

  const renderItem = ({ item }: { item: User }) => (
    <TouchableOpacity style={styles.item}>
      <Text style={styles.name}>{item.name}</Text>
      <Text style={styles.email}>{item.email}</Text>
    </TouchableOpacity>
  );

  return (
    <FlatList
      data={users}
      renderItem={renderItem}
      keyExtractor={(item) => item.id}
      refreshing={loading}
      onRefresh={fetchUsers}
    />
  );
};

const styles = StyleSheet.create({
  item: { padding: 16, borderBottomWidth: 1, borderBottomColor: '#eee' },
  name: { fontSize: 18, fontWeight: 'bold' },
  email: { color: '#666' },
});
```

### Navigation
```tsx
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

const Stack = createNativeStackNavigator();
const Tab = createBottomTabNavigator();

function HomeTabs() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Feed" component={FeedScreen} />
      <Tab.Screen name="Profile" component={ProfileScreen} />
      <Tab.Screen name="Settings" component={SettingsScreen} />
    </Tab.Navigator>
  );
}

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Home" component={HomeTabs} />
        <Stack.Screen name="Details" component={DetailsScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

### State Management (Zustand)
```tsx
import { create } from 'zustand';

interface TodoState {
  todos: Todo[];
  addTodo: (text: string) => void;
  toggleTodo: (id: string) => void;
  removeTodo: (id: string) => void;
}

export const useTodoStore = create<TodoState>((set) => ({
  todos: [],
  addTodo: (text) => set((state) => ({
    todos: [...state.todos, { id: Date.now().toString(), text, done: false }]
  })),
  toggleTodo: (id) => set((state) => ({
    todos: state.todos.map(t => t.id === id ? { ...t, done: !t.done } : t)
  })),
  removeTodo: (id) => set((state) => ({
    todos: state.todos.filter(t => t.id !== id)
  })),
}));
```

## Flutter

### Widget Structure
```dart
import 'package:flutter/material.dart';

class UserList extends StatefulWidget {
  @override
  _UserListState createState() => _UserListState();
}

class _UserListState extends State<UserList> {
  List<User> users = [];
  bool loading = true;

  @override
  void initState() {
    super.initState();
    fetchUsers();
  }

  Future<void> fetchUsers() async {
    final response = await http.get(Uri.parse('https://api.example.com/users'));
    setState(() {
      users = (jsonDecode(response.body) as List)
          .map((e) => User.fromJson(e))
          .toList();
      loading = false;
    });
  }

  @override
  Widget build(BuildContext context) {
    return ListView.builder(
      itemCount: users.length,
      itemBuilder: (context, index) {
        final user = users[index];
        return ListTile(
          title: Text(user.name),
          subtitle: Text(user.email),
          onTap: () => Navigator.push(
            context,
            MaterialPageRoute(builder: (_) => UserDetail(user: user)),
          ),
        );
      },
    );
  }
}
```

### State Management (Riverpod)
```dart
import 'package:flutter_riverpod/flutter_riverpod.dart';

final userProvider = FutureProvider<List<User>>((ref) async {
  final response = await http.get(Uri.parse('https://api.example.com/users'));
  return (jsonDecode(response.body) as List)
      .map((e) => User.fromJson(e))
      .toList();
});

class UserList extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final usersAsync = ref.watch(userProvider);
    
    return usersAsync.when(
      data: (users) => ListView.builder(
        itemCount: users.length,
        itemBuilder: (context, index) => UserTile(user: users[index]),
      ),
      loading: () => CircularProgressIndicator(),
      error: (err, stack) => Text('Error: $err'),
    );
  }
}
```

## Mobile-Specific Patterns

### Push Notifications
```javascript
// React Native - Firebase
import messaging from '@react-native-firebase/messaging';

async function requestPermission() {
  const auth = await messaging().requestPermission();
  if (auth === messaging.AuthorizationStatus.AUTHORIZED) {
    const token = await messaging().getToken();
    console.log('FCM Token:', token);
  }
}

messaging().onMessage(async remoteMessage => {
  console.log('Foreground message:', remoteMessage);
});
```

### Offline Storage
```typescript
// React Native - AsyncStorage
import AsyncStorage from '@react-native-async-storage/async-storage';

const storeData = async (key: string, value: any) => {
  try {
    await AsyncStorage.setItem(key, JSON.stringify(value));
  } catch (e) {
    console.error('Storage error:', e);
  }
};

const getData = async (key: string) => {
  try {
    const value = await AsyncStorage.getItem(key);
    return value ? JSON.parse(value) : null;
  } catch (e) {
    return null;
  }
};
```

## Best Practices
- Use TypeScript for React Native
- Implement proper error boundaries
- Handle platform-specific code (iOS/Android)
- Optimize images and assets
- Use proper navigation patterns
- Implement offline-first architecture
- Test on real devices
- Follow platform guidelines (Material/Cupertino)
