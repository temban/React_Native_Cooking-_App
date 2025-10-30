# 🍳 Smart Cooking App - Complete Beginner's Tutorial

## 📚 Table of Contents
1. [Project Overview](#project-overview)
2. [Architecture Diagram](#architecture-diagram)
3. [File Structure Explained](#file-structure-explained)
4. [Step-by-Step Code Explanation](#step-by-step-code-explanation)
5. [Key Concepts for Beginners](#key-concepts-for-beginners)
6. [Teaching Guide](#teaching-guide)

---

## 🎯 Project Overview

### What We're Building
A **Smart Cooking Assistant** mobile app that helps users:
- **Discover** new recipes
- **Manage** their pantry
- **Save** favorite recipes
- **Customize** dietary preferences

### Tech Stack
- **React Native** - Mobile app framework
- **Expo** - Development platform
- **TypeScript** - Type-safe JavaScript
- **Expo Router** - Navigation system

---

## 🏗️ Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    SMART COOKING APP                        │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────┐    ┌─────────────────┐                 │
│  │    Discover     │    │     Pantry      │                 │
│  │    Screen       │    │     Screen      │                 │
│  └─────────────────┘    └─────────────────┘                 │
│          │                        │                         │
│  ┌─────────────────┐    ┌─────────────────┐                 │
│  │   Recipes       │    │ User Settings   │                 │
│  │   Screen        │    │    Screen       │                 │
│  └─────────────────┘    └─────────────────┘                 │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│                 STATE MANAGEMENT & DATA                     │
│                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │   User      │  │   Recipe    │  │   Mock      │          │
│  │ Preferences │  │    Data     │  │    API      │          │
│  │   Hook      │  │             │  │             │          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Data Flow
```
User Interaction → Component → Hook/State → UI Update
      ↓              ↓            ↓           ↓
   Tap Button → Update State → Save Data → Show Changes
```

---

## 📁 File Structure Explained

### 🎒 Root Level Files
```typescript
// package.json - Project Dependencies
{
  "dependencies": {
    "expo": "~50.0.0",          // Mobile development platform
    "expo-router": "~3.4.0",    // File-based navigation
    "react": "18.2.0",          // UI library
    "react-native": "0.73.0",   // Mobile framework
    "@expo/vector-icons": "^14.0.0" // Icons library
  }
}
```

```typescript
// app.json - App Configuration
{
  "expo": {
    "name": "Smart Cooking App",
    "slug": "smart-cooking-app",
    "plugins": ["expo-router"]  // Enable file-based routing
  }
}
```

```typescript
// tsconfig.json - TypeScript Configuration
{
  "extends": "expo/tsconfig.base",
  "compilerOptions": {
    "strict": true,             // Enable strict type checking
    "paths": {
      "@/*": ["./*"]           // Map @/ to root directory
    }
  }
}
```

### 🗂️ App Folder (Navigation & Screens)
```
app/
├── _layout.tsx          # Root navigation layout
├── (tabs)/              # Bottom tab navigation
│   ├── _layout.tsx      # Tab configuration
│   ├── index.tsx        # Discover screen (default tab)
│   ├── pantry.tsx       # Pantry management screen
│   └── recipes.tsx      # Personal recipes screen
└── user-settings.tsx    # User preferences modal
```

### 🧩 Components (Reusable UI Pieces)
```
components/ui/
├── Header.tsx           # App header with title & profile
├── RecipeCard.tsx       # Individual recipe display
└── RecipeGrid.tsx       # Recipe layout manager
```

### 🎨 Styling & Configuration
```
constants/Colors.ts      # Color definitions for light/dark mode
hooks/                   # Custom React hooks for state management
data/mockData.ts         # Sample recipe data
services/api.ts          # Backend communication setup
```

---

## 🔍 Step-by-Step Code Explanation

### 1. 🎯 Root Layout (`app/_layout.tsx`)
```typescript
import { Stack } from 'expo-router';
import { UserPreferencesProvider } from '@/hooks/useUserPreferences';

export default function RootLayout() {
  return (
    <UserPreferencesProvider>
      <Stack screenOptions={{ headerShown: false }}>
        <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
        <Stack.Screen name="user-settings" options={{ presentation: 'modal' }} />
      </Stack>
    </UserPreferencesProvider>
  );
}
```

**🔍 Line-by-Line Explanation:**
- `Stack` - Navigation container that manages screens
- `UserPreferencesProvider` - Wraps entire app to share user settings
- `headerShown: false` - Hides default header (we use custom headers)
- `(tabs)` - Loads the bottom tab navigation
- `user-settings` - Opens as a modal (slides up from bottom)

**🎓 Teaching Point:** This is like a "master controller" that sets up the app's navigation structure.

### 2. 🗂️ Tab Navigation (`app/(tabs)/_layout.tsx`)
```typescript
export default function TabLayout() {
  const colorScheme = useColorScheme();
  
  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: Colors[colorScheme ?? 'light'].tint,
      }}>
      <Tabs.Screen
        name="index"
        options={{
          title: 'Discover',
          tabBarIcon: ({ color, focused }) => (
            <Ionicons name={focused ? 'compass' : 'compass-outline'} color={color} size={24} />
          ),
        }}
      />
      {/* ... other tabs */}
    </Tabs>
  );
}
```

**🔍 Key Concepts:**
- `Tabs` - Creates bottom navigation bar
- `useColorScheme()` - Detects phone's dark/light mode
- `tabBarIcon` - Shows different icons for active/inactive states
- `focused ? 'compass' : 'compass-outline'` - Changes icon based on selection

**🎓 Teaching Point:** This is like a "remote control" with buttons to switch between app sections.

### 3. 🏠 Discover Screen (`app/(tabs)/index.tsx`)
```typescript
export default function DiscoverScreen() {
  const router = useRouter();
  const colorScheme = useColorScheme();
  const [activeFilter, setActiveFilter] = useState('All');

  const filters = ['All', 'Appetizers', 'Main Courses', 'Desserts'];
  
  return (
    <SafeAreaView style={styles.safeArea}>
      <ScrollView style={styles.container}>
        <Header 
          title="Discover" 
          onProfilePress={() => router.push('/user-settings')}
        />
        
        {/* Search Bar */}
        <View style={styles.searchBar}>
          <View style={styles.searchSection}>
            <Ionicons name="search" size={18} color={colors.icon} />
            <TextInput
              style={styles.searchInput}
              placeholder="Search"
              placeholderTextColor={colors.icon}
            />
          </View>
          {/* ... Time and Pantry sections */}
        </View>

        {/* Recipe Grids */}
        <RecipeGrid recipes={mockRecipes.popular} title="Most Popular" />
      </ScrollView>
    </SafeAreaView>
  );
}
```

**🔍 State Management:**
```typescript
const [activeFilter, setActiveFilter] = useState('All');
```
- `useState` - React hook to remember data between screen refreshes
- `activeFilter` - Current selected filter ('All', 'Appetizers', etc.)
- `setActiveFilter` - Function to update the filter

**🔍 Navigation:**
```typescript
const router = useRouter();
onProfilePress={() => router.push('/user-settings')}
```
- `useRouter()` - Gets navigation controller
- `router.push()` - Navigates to different screen

**🎓 Teaching Point:** This screen is like a "recipe magazine" - it shows different categories and lets you browse.

### 4. 🎴 Recipe Card Component (`components/ui/RecipeCard.tsx`)
```typescript
export default function RecipeCard({ recipe, onPress }: RecipeCardProps) {
  const colorScheme = useColorScheme();
  const colors = Colors[colorScheme ?? 'light'];

  const getCardSize = () => {
    switch (recipe.gridType) {
      case '4x20':
        return { width: 150, height: 175, imageHeight: 120 };
      case '3x20':
        return { width: 200, height: 200, imageHeight: 140 };
      case '1x25':
        return { width: screenWidth * 0.8, height: 300, imageHeight: 180 };
    }
  };

  const { width, height, imageHeight } = getCardSize();

  return (
    <TouchableOpacity style={[styles.card, { width, height }]} onPress={onPress}>
      <Image source={{ uri: recipe.image }} style={[styles.image, { height: imageHeight }]} />
      <View style={styles.info}>
        <Text style={styles.title}>{recipe.title}</Text>
        <Text style={styles.meta}>{recipe.time} • {recipe.difficulty}</Text>
      </View>
    </TouchableOpacity>
  );
}
```

**🔍 Props System:**
```typescript
interface RecipeCardProps {
  recipe: Recipe;        // Recipe data to display
  onPress?: () => void;  // Function to call when card is tapped
}
```

**🔍 Dynamic Sizing:**
```typescript
const getCardSize = () => {
  switch (recipe.gridType) {
    case '4x20': return { width: 150, height: 175 };
    // ... other cases
  }
};
```

**🎓 Teaching Point:** This is like a "recipe trading card" - it displays recipe information in a consistent format.

### 5. 📊 Recipe Grid Component (`components/ui/RecipeGrid.tsx`)
```typescript
export default function RecipeGrid({ recipes, title, showTitle = true, marginBottom = 10 }: RecipeGridProps) {
  const renderGrid = () => {
    const gridType = recipes[0]?.gridType;
    
    switch (gridType) {
      case '4x20': return render4x20Grid();
      case '3x20': return render3x20Grid();
      case '1x25': return render1x25Grid();
    }
  };

  const render4x20Grid = () => (
    <ScrollView horizontal showsHorizontalScrollIndicator={false}>
      <View style={styles.rowContainer}>
        {recipes.map((recipe) => (
          <RecipeCard key={recipe.id} recipe={recipe} />
        ))}
      </View>
    </ScrollView>
  );

  return (
    <View style={styles.container}>
      {showTitle && <Text style={styles.title}>{title}</Text>}
      {renderGrid()}
    </View>
  );
}
```

**🔍 Conditional Rendering:**
```typescript
{showTitle && <Text style={styles.title}>{title}</Text>}
```
- Only shows title if `showTitle` is true
- `&&` means "render this only if condition is true"

**🔍 Array Mapping:**
```typescript
{recipes.map((recipe) => (
  <RecipeCard key={recipe.id} recipe={recipe} />
))}
```
- `.map()` - Creates a RecipeCard for each recipe in the array
- `key={recipe.id}` - Helps React efficiently update the list

**🎓 Teaching Point:** This is like a "shelf organizer" - it arranges recipe cards in different layouts.

### 6. 💾 State Management (`hooks/useUserPreferences.ts`)
```typescript
export function UserPreferencesProvider({ children }: { children: ReactNode }) {
  const [preferences, setPreferences] = useState<UserPreferences>({
    focus: 'All',
    dietaryPreference: 'All',
    restrictions: [],
    kitchenAppliances: [],
  });

  const updatePreference = (key: keyof UserPreferences, value: any) => {
    setPreferences(prev => ({ ...prev, [key]: value }));
  };

  const toggleRestriction = (restriction: string) => {
    setPreferences(prev => ({
      ...prev,
      restrictions: prev.restrictions.includes(restriction)
        ? prev.restrictions.filter(r => r !== restriction)
        : [...prev.restrictions, restriction]
    }));
  };

  return (
    <UserPreferencesContext.Provider value={{ preferences, updatePreference, toggleRestriction }}>
      {children}
    </UserPreferencesContext.Provider>
  );
}
```

**🔍 Context Provider Pattern:**
- `UserPreferencesProvider` - Wraps the app to share state
- `UserPreferencesContext.Provider` - Makes state available to all components
- `value={{ preferences, updatePreference }}` - What gets shared

**🔍 State Update Pattern:**
```typescript
setPreferences(prev => ({ ...prev, [key]: value }));

```
- `prev` - Current state
- `...prev` - Copy all existing properties
- `[key]: value` - Update specific property

**🎓 Teaching Point:** This is like a "shared notebook" that all components can read from and write to.

---

## 🎓 Key Concepts for Beginners

### 1. 🧩 Components
**What:** Reusable pieces of UI
**Example:** `RecipeCard`, `Header`, `RecipeGrid`
**Analogy:** Like Lego blocks - build complex UIs from simple pieces

### 2. 📱 Screens
**What:** Full pages in your app  
**Example:** `DiscoverScreen`, `RecipesScreen`
**Analogy:** Like rooms in a house - each has a different purpose

### 3. 🧭 Navigation
**What:** Moving between screens
**Example:** `router.push('/user-settings')`
**Analogy:** Like walking between rooms

### 4. 🎨 Props
**What:** Data passed to components
**Example:** `<RecipeCard recipe={recipe} />`
**Analogy:** Like giving instructions to someone

### 5. 💾 State
**What:** Data that can change
**Example:** `const [activeFilter, setActiveFilter] = useState('All')`
**Analogy:** Like memory - remembers user choices

### 6. 🎯 Hooks
**What:** Functions that let you use React features
**Example:** `useState`, `useEffect`, `useContext`
**Analogy:** Like tools in a toolbox

---

## 👨‍🏫 Teaching Guide

### Week 1: Foundation
**Day 1-2:** Explain React Native basics
- What are components?
- JSX syntax
- Basic styling

**Day 3-4:** Build simple components
- Create a basic RecipeCard
- Understand props

### Week 2: Navigation & Data
**Day 1-2:** App structure
- File-based routing
- Tab navigation

**Day 3-4:** Data flow
- Passing data between components
- Using mock data

### Week 3: State Management
**Day 1-2:** Local state
- `useState` hook
- User interactions

**Day 3-4:** Global state
- Context API
- User preferences

### Week 4: Advanced Features
**Day 1-2:** Advanced components
- RecipeGrid with different layouts
- Conditional rendering

**Day 3-4:** Polish & deploy
- Styling improvements
- Building the app

### 🎯 Teaching Tips

1. **Start Simple** - Begin with static components before adding state
2. **Build Incrementally** - Add one feature at a time
3. **Use Analogies** - Relate programming concepts to real-world examples
4. **Hands-On Practice** - Let the student type code themselves
5. **Debug Together** - Show how to fix common errors

### ❓ Common Beginner Questions

**Q: Why do we need `key` prop in lists?**
**A:** It helps React efficiently update only changed items, like bookmarks in a book.

**Q: What's the difference between `View` and `ScrollView`?**
**A:** `View` is a container, `ScrollView` lets you scroll content that doesn't fit on screen.

**Q: Why use TypeScript?**
**A:** It catches errors before running the app, like spell-check for code.

---

## 🚀 Next Steps for Student

1. **Modify card designs** - Change colors, sizes, layouts
2. **Add new filters** - Implement search functionality  
3. **Create new screens** - Add recipe detail view
4. **Connect to real API** - Replace mock data with live data
5. **Add animations** - Make interactions smoother

This tutorial provides a solid foundation for understanding React Native development through the lens of a practical, real-world project! 🎉

## Get started

1. Install dependencies

   ```bash
   npm install
   ```

2. Start the app

   ```bash
   npx expo start
   ```

In the output, you'll find options to open the app in a

- [development build](https://docs.expo.dev/develop/development-builds/introduction/)
- [Android emulator](https://docs.expo.dev/workflow/android-studio-emulator/)
- [iOS simulator](https://docs.expo.dev/workflow/ios-simulator/)
- [Expo Go](https://expo.dev/go), a limited sandbox for trying out app development with Expo

You can start developing by editing the files inside the **app** directory. This project uses [file-based routing](https://docs.expo.dev/router/introduction).

## Get a fresh project

When you're ready, run:

```bash
npm run reset-project
```

This command will move the starter code to the **app-example** directory and create a blank **app** directory where you can start developing.

## Learn more

To learn more about developing your project with Expo, look at the following resources:

- [Expo documentation](https://docs.expo.dev/): Learn fundamentals, or go into advanced topics with our [guides](https://docs.expo.dev/guides).
- [Learn Expo tutorial](https://docs.expo.dev/tutorial/introduction/): Follow a step-by-step tutorial where you'll create a project that runs on Android, iOS, and the web.

## Join the community

Join our community of developers creating universal apps.

- [Expo on GitHub](https://github.com/expo/expo): View our open source platform and contribute.
- [Discord community](https://chat.expo.dev): Chat with Expo users and ask questions.


## Images
![photo_1_2025-10-30_15-10-13](https://github.com/user-attachments/assets/3802a642-dc5c-4ee2-99de-3bc721ee4b97) ![photo_2_2025-10-30_15-10-13](https://github.com/user-attachments/assets/265088f7-fb6b-4096-b5db-80e2e9c72d03) ![photo_3_2025-10-30_15-10-13](https://github.com/user-attachments/assets/48b0ebfe-764a-4f26-9f2f-64552077274b)


![photo_5_2025-10-30_15-10-13](https://github.com/user-attachments/assets/129a770b-8bd7-4969-a955-0eef59161c59) ![photo_6_2025-10-30_15-10-13](https://github.com/user-attachments/assets/1c3f2aa7-47d5-43cf-bf50-1dc6e392331f)

![photo_7_2025-10-30_15-10-13](https://github.com/user-attachments/assets/74ba1fec-8c88-46b0-9951-b835a7cfe1f2) ![photo_8_2025-10-30_15-10-13](https://github.com/user-attachments/assets/dda254e9-e448-425e-9ab1-91d6f138e1de)

![photo_4_2025-10-30_15-10-13](https://github.com/user-attachments/assets/52552fb0-1015-416e-8237-6670b91058d6)

