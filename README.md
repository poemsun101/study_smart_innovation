📘 Project Title:
EduSmart – AI-Driven Smart Education App

🧩 Project Setup Overview
1. Tech Stack
Layer	Technology
Frontend	Kotlin + Jetpack Compose
Backend	Firebase (Auth, Firestore, Storage, Analytics)
AI Module	Local ML logic (Kotlin-based) + Firebase data
Architecture	MVVM (Model-View-ViewModel)
⚙️ Step 1: Create Project in Android Studio
Open Android Studio → “New Project”.

Choose Empty Compose Activity.

Name → EduSmart

Package name → com.example.edusmart

Language → Kotlin

Minimum SDK → Android 8.0 (API 26)

🧠 Step 2: Add Dependencies in build.gradle (Module)
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
    id 'com.google.gms.google-services'
}

android {
    namespace "com.example.edusmart"
    compileSdk 35

    defaultConfig {
        applicationId "com.example.edusmart"
        minSdk 26
        targetSdk 35
        versionCode 1
        versionName "1.0"
    }

    buildFeatures {
        compose true
    }

    composeOptions {
        kotlinCompilerExtensionVersion = "1.5.3"
    }
}

dependencies {
    implementation("androidx.core:core-ktx:1.13.1")
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.8.3")
    implementation("androidx.activity:activity-compose:1.9.2")
    implementation(platform("androidx.compose:compose-bom:2024.06.00"))
    implementation("androidx.compose.ui:ui")
    implementation("androidx.compose.material3:material3")
    implementation("androidx.navigation:navigation-compose:2.8.0")

    // Firebase
    implementation(platform("com.google.firebase:firebase-bom:33.2.0"))
    implementation("com.google.firebase:firebase-auth-ktx")
    implementation("com.google.firebase:firebase-firestore-ktx")
    implementation("com.google.firebase:firebase-storage-ktx")

    // Coil for image/video thumbnails
    implementation("io.coil-kt:coil-compose:2.6.0")

    // Coroutine + ViewModel
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.8.3")
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.8.1")

    // AI/ML utilities (basic stats)
    implementation("org.jetbrains.kotlinx:kotlinx-collections-immutable:0.3.6")
}
🔥 Step 3: Connect Firebase
Visit Firebase Console

Create new project → EduSmart

Add Android app → package name com.example.edusmart

Download google-services.json → place it inside /app

Enable:

Authentication → Email/Google sign-in

Firestore Database

Storage

🧾 Step 4: Folder Structure (MVVM)
com.example.edusmart/
├── data/
│   ├── model/
│   │   ├── User.kt
│   │   ├── Quiz.kt
│   │   └── Score.kt
│   ├── repository/
│       └── FirebaseRepository.kt
├── ui/
│   ├── theme/
│   ├── screens/
│   │   ├── LoginScreen.kt
│   │   ├── DashboardScreen.kt
│   │   ├── QuizScreen.kt
│   │   ├── LeaderboardScreen.kt
│   │   └── VideoPlayerScreen.kt
│   ├── components/
│       └── CommonComponents.kt
├── viewmodel/
│   ├── AuthViewModel.kt
│   ├── QuizViewModel.kt
│   └── AIRecommendationViewModel.kt
└── MainActivity.kt
🧩 Step 5: Key Modules
✅ 1. Firebase Authentication (Login/Register)
// AuthViewModel.kt
class AuthViewModel : ViewModel() {
    private val auth = FirebaseAuth.getInstance()
    val user = mutableStateOf(auth.currentUser)

    fun login(email: String, password: String, onResult: (Boolean) -> Unit) {
        auth.signInWithEmailAndPassword(email, password)
            .addOnCompleteListener { onResult(it.isSuccessful) }
    }

    fun register(email: String, password: String, onResult: (Boolean) -> Unit) {
        auth.createUserWithEmailAndPassword(email, password)
            .addOnCompleteListener { onResult(it.isSuccessful) }
    }

    fun logout() {
        auth.signOut()
        user.value = null
    }
}
✅ 2. Firestore Data Model
data class Quiz(
    val question: String = "",
    val options: List<String> = emptyList(),
    val correctIndex: Int = 0,
    val topic: String = ""
)
✅ 3. AI-Driven Recommendation
Simple local rule-based logic using quiz performance:

class AIRecommendationViewModel : ViewModel() {
    fun getRecommendation(topicScores: Map<String, Int>): String {
        val weakest = topicScores.minByOrNull { it.value }?.key ?: "General"
        return "We noticed you need improvement in $weakest. Try watching topic videos and retake the quiz!"
    }
}
✅ 4. Leaderboard Screen (Firestore Realtime Update)
@Composable
fun LeaderboardScreen() {
    val db = FirebaseFirestore.getInstance()
    var scores by remember { mutableStateOf(listOf<Pair<String, Int>>()) }

    LaunchedEffect(Unit) {
        db.collection("scores").addSnapshotListener { snap, _ ->
            val data = snap?.documents?.mapNotNull {
                val name = it.getString("username")
                val score = it.getLong("score")?.toInt()
                if (name != null && score != null) name to score else null
            } ?: emptyList()
            scores = data.sortedByDescending { it.second }
        }
    }

    Column(Modifier.fillMaxSize().padding(16.dp)) {
        Text("Leaderboard", style = MaterialTheme.typography.headlineMedium)
        scores.forEachIndexed { index, (name, score) ->
            Text("${index + 1}. $name — $score pts")
        }
    }
}
✅ 5. Video Player (Firebase Storage URL)
Use ExoPlayer:

@Composable
fun VideoPlayerScreen(videoUrl: String) {
    val context = LocalContext.current
    val exoPlayer = remember {
        ExoPlayer.Builder(context).build().apply {
            setMediaItem(MediaItem.fromUri(videoUrl))
            prepare()
            playWhenReady = true
        }
    }

    AndroidView(factory = {
        PlayerView(it).apply {
            player = exoPlayer
        }
    })
}
🚀 Step 6: AI-Enhanced Learning Dashboard
Your DashboardScreen will display:

User’s current score & progress

Recommended topics (from AI module)

Quick links to quizzes and videos

📊 Project/Research Outcomes
No.	Project / Research Outcome	Description
1	Enhanced Learning Experience	Interactive AI-driven personalized education through quizzes, video tutorials, and feedback analytics.
2	Accessible and Inclusive Education	24/7 access to digital learning materials through cloud-connected mobile app.
3	Data-Driven Learning Insights	Firebase + AI generate performance trends and personalized study recommendations.
4	Promotion of Digital Literacy and Innovation	Encourages adoption of Kotlin, Firebase, and AI technologies among educators and students.
🌍 Selected SDG Goals
Goal 4: Quality Education

Goal 9: Industry, Innovation and Infrastructure

Goal 10: Reduced Inequality

Goal 11: Sustainable Cities and Communities


You said:
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>   package com.example.educationsmart

import android.os.Bundle
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_main)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
    }
}  
<!-- Failed to upload "WhatsApp Video 2025-11-29 at 11.53.11_4a9e052a.mp4" -->

![Image](https://github.com/user-attachments/assets/5cec9122-abaa-4e10-9c3c-2dfcc008d4e7)                    ![Image](https://github.com/user-attachments/assets/ced83e66-6ea2-483d-a602-73720575220c)

![Image](https://github.com/user-attachments/assets/e9fa3e62-d78d-4451-97f5-5b846901f6de)                    ![Image](https://github.com/user-attachments/assets/74f823d1-b245-4c0e-84b0-15f1e8f2709b)

![Image](https://github.com/user-attachments/assets/22d9a7ba-4f0c-407b-8a48-30b5adb34667)                    ![Image](https://github.com/user-attachments/assets/93f7b0ef-b87d-4cb6-993f-0f6df864d5fc)

