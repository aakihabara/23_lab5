<p align = "center">МИНИСТЕРСТВО НАУКИ И ВЫСШЕГО ОБРАЗОВАНИЯ<br>
РОССИЙСКОЙ ФЕДЕРАЦИИ<br>
ФЕДЕРАЛЬНОЕ ГОСУДАРСТВЕННОЕ БЮДЖЕТНОЕ<br>
ОБРАЗОВАТЕЛЬНОЕ УЧРЕЖДЕНИЕ ВЫСШЕГО ОБРАЗОВАНИЯ<br>
«САХАЛИНСКИЙ ГОСУДАРСТВЕННЫЙ УНИВЕРСИТЕТ»</p>
<br><br><br><br><br><br>
<p align = "center">Институт естественных наук и техносферной безопасности<br>Кафедра информатики<br>Коньков Никита Алексеевич</p>
<br><br><br>
<p align = "center">Лабораторная работа №5<br><strong>«Вторая activity».</strong><br>01.03.02 Прикладная математика и информатика</p>
<br><br><br><br><br><br><br><br><br><br><br><br>
<p align = "right">Научный руководитель<br>
Соболев Евгений Игоревич</p>
<br><br><br>
<p align = "center">г. Южно-Сахалинск<br>2022 г.</p>
<br><br><br><br><br><br><br><br><br><br><br><br>

<h1 align = "center">Введение</h1>

<p><b>Android Studio</b> — интегрированная среда разработки (IDE) для работы с платформой Android, анонсированная 16 мая 2013 года на конференции Google I/O. В последней версии Android Studio поддерживается Android 4.1 и выше.</p>
<p><b>Kotlin</b> — это кроссплатформенный статически типизированный язык программирования общего назначения высокого уровня. Kotlin предназначен для полного взаимодействия с Java, а версия стандартной библиотеки Kotlin для JVM зависит от библиотеки классов Java, но вывод типов позволяет сделать ее синтаксис более кратким. Kotlin в основном нацелен на JVM, но также компилируется в JavaScript (например, для интерфейсных веб-приложений, использующих React) или собственный код через LLVM (например, для собственных приложений iOS, разделяющих бизнес-логику с приложениями Android). Затраты на разработку языка несет JetBrains, а Kotlin Foundation защищает торговую марку Kotlin.</p>

<br>
<h1 align = "center">Цели и задачи</h1>
<p>1. <b>Упражнение. Лазейка для читера. </b> Мошенники никогда не выигрывают... Если, конечно, им не удастся обойти вашу защиту от мошенничества. А скорее всего, они так и сделают — именно потому, что они мошенники. У GeoQuiz есть кое-какая лазейка. Пользователи могут вращать CheatActivity после чита, чтобы удалить следы обмана. После возврата к MainActivity их жульничество будет забыто. Исправьте эту ошибку, сохраняя состояние пользовательского интерфейса CheatActivity во время вращения и после уничтожения процесса. </p>
<p>2. <b>Упражнение. Отслеживание читов по вопросу. </b> В настоящее время, когда пользователь читерит на одном вопросе, он считается читером по всем вопросам. Обновите GeoQuiz, чтобы отслеживать, сколько раз пользователь нарушал закон. Когда пользователь использует чит для ответа на заданный вопрос, осуждайте его всякий раз, когда он пытается ответить на этот вопрос. Когда пользователь отвечает на вопрос, с которым он не жульничал, покажите правильный или неправильный ответ. </p>

<p><a href="https://www.codewars.com/kata/583710ccaa6717322c000105">3. Simple multiplication</a></p>
<p><a href="https://www.codewars.com/kata/57eae20f5500ad98e50002c5">4. Remove String Spaces</a></p>
<p><a href="https://www.codewars.com/kata/57a0e5c372292dd76d000d7e">5. String repeat</a></p>
<p><a href="https://www.codewars.com/kata/523b4ff7adca849afe000035">6. Function 1 - hello world</a></p>
<p><a href="https://www.codewars.com/kata/515e271a311df0350d00000f">7. Square(n) Sum</a></p>
<p><a href="https://www.codewars.com/kata/5a3fe3dde1ce0e8ed6000097">8. Century From Year</a></p>
<p><a href="https://www.codewars.com/kata/5545f109004975ea66000086">9. Is n divisible by x and y?</a></p>
<p><a href="https://www.codewars.com/kata/53da3dbb4a5168369a0000fe">10. Even or Odd</a></p>

<h1 align = "center">Решение</h1>

<h2 align = "center">файл MainActivity</h2>

```kotlin
package com.example.secondapp

import android.app.Activity
import android.content.Intent
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import android.widget.Button
import android.widget.ImageButton
import android.widget.TextView
import android.widget.Toast
import androidx.lifecycle.ViewModelProvider
import androidx.lifecycle.ViewModelProviders
import java.security.KeyStore.TrustedCertificateEntry

private const val TAG = "MainActivity"
private const val KEY_INDEX = "index"
private const val REQUEST_CODE_CHEAT = 0

class MainActivity : AppCompatActivity() {

    private lateinit var true_button: Button
    private lateinit var false_button:Button
    private lateinit var nextButton: ImageButton
    private lateinit var prevButton: ImageButton
    private lateinit var questionTextView: TextView
    private lateinit var cheatButton: Button

    private var userAnswer = false;

    private val quizViewModel: QuizViewModel by lazy {
        ViewModelProviders.of(this).get(QuizViewModel::class.java)
    }

    private fun toastMessage(text: String){
        Toast.makeText(this, text, Toast.LENGTH_SHORT).show()
    }

    private fun checkIfFinish(){
        var finishing = true
        for(i in quizViewModel.isAnswered) {
            if (i == 0) {
                finishing = false
            }
        }

        if(finishing){
            var count = 0

            for(i in quizViewModel.correctAnswer){
                if(i){
                    count++
                }
            }

            var result = count * 100 / quizViewModel.currentQuestionSize

            Toast.makeText(this, "$result% correct answers", Toast.LENGTH_SHORT).show()
        }

    }


    private fun checkOnAvaliableButtons(){
        if(checkOnAnswered()){
            true_button.isEnabled = false
            false_button.isEnabled = false
        }
        else {
            true_button.isEnabled = true
            false_button.isEnabled = true
        }
    }

    private fun buildTextView(){
        val questionTextResId = quizViewModel.currentQuestionText
        questionTextView.setText(questionTextResId)
    }

    private fun checkOnAnswered() : Boolean{
        return quizViewModel.isAnswered[quizViewModel.currentIndex] == 1
    }

    private fun checkOnNPButtons(){
        when (quizViewModel.currentIndex) {
            0 -> {

            }
            (quizViewModel.currentQuestionSize - 1) -> {
                nextButton.isEnabled = false
                questionTextView.isClickable = false
                prevButton.isEnabled = true
            }
            else -> {
                nextButton.isEnabled = true
                questionTextView.isClickable = true
                prevButton.isEnabled = true
            }
        }
    }

    private fun checkAnswer(userAnswer: Boolean){
        val correctAnswer: Boolean = quizViewModel.currentQuestionAnswer

        val messageResId = when {
            quizViewModel.isCheated[quizViewModel.currentIndex] -> R.string.judgment_toast
            userAnswer == correctAnswer -> R.string.correct_toast
            else -> R.string.incorrect_toast
        }
        Toast.makeText(this, messageResId, Toast.LENGTH_SHORT).show()
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(TAG, "onCreate(Bundle?) called")
        setContentView(R.layout.activity_main)

        val currentIndex = savedInstanceState?.getInt(KEY_INDEX, 0) ?: 0
        quizViewModel.currentIndex = currentIndex

        true_button = findViewById(R.id.true_button)
        false_button = findViewById(R.id.false_button)
        prevButton = findViewById(R.id.prev_button)
        nextButton = findViewById(R.id.next_button)
        questionTextView = findViewById(R.id.question_text_view)
        cheatButton = findViewById(R.id.cheat_button)

        checkOnAvaliableButtons()
        checkOnNPButtons()

        true_button.setOnClickListener{

            checkAnswer(true)

            quizViewModel.correctAnswer[quizViewModel.currentIndex] = quizViewModel.currentQuestionAnswer

            quizViewModel.isAnswered[quizViewModel.currentIndex] = 1

            checkOnAvaliableButtons()

            checkIfFinish()
        }

        false_button.setOnClickListener{

            checkAnswer(false)

            quizViewModel.correctAnswer[quizViewModel.currentIndex] = !quizViewModel.currentQuestionAnswer

            quizViewModel.isAnswered[quizViewModel.currentIndex] = 1

            checkOnAvaliableButtons()

            checkIfFinish()

        }

        cheatButton.setOnClickListener {
            val answerIsTrue = quizViewModel.currentQuestionAnswer
            val intent = CheatActivity.newIntent(this@MainActivity, answerIsTrue)
            startActivityForResult(intent, REQUEST_CODE_CHEAT)
        }

        val questionTextResId = quizViewModel.currentQuestionText
        questionTextView.setText(questionTextResId)

        questionTextView.setOnClickListener{
            quizViewModel.moveToNext()
            checkOnAvaliableButtons()
            buildTextView()
            checkOnNPButtons()
        }

        nextButton.setOnClickListener{
            quizViewModel.moveToNext()
            checkOnAvaliableButtons()
            buildTextView()
            checkOnNPButtons()
        }

        prevButton.setOnClickListener{
            quizViewModel.moveToPrev()
            checkOnAvaliableButtons()
            buildTextView()
            checkOnNPButtons()
        }
    }

    override fun onStart() {
        super.onStart()
        Log.d(TAG, "onStart(Bundle?) called")
    }

    override fun onResume() {
        super.onResume()
        Log.d(TAG, "onResume(Bundle?) called")
    }

    override fun onPause() {
        super.onPause()
        Log.d(TAG, "onPause(Bundle?) called")
    }

    override fun onSaveInstanceState(savedInstanceState: Bundle) {
        super.onSaveInstanceState(savedInstanceState)
        Log.i(TAG, "onSaveInstanceState")
        savedInstanceState.putInt(KEY_INDEX, quizViewModel.currentIndex)
    }

    override fun onStop(){
        super.onStop()
        Log.d(TAG, "onStop(Bundle?) called")
    }

    override fun onDestroy() {
        super.onDestroy()
        Log.d(TAG, "onDestroy(Bundle?) called")
    }

    override fun onActivityResult(requestCode: Int,
                                  resultCode: Int,
                                  data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)

        if(resultCode != Activity.RESULT_OK){
            return
        }

        if(requestCode == REQUEST_CODE_CHEAT){
            quizViewModel.isCheated[quizViewModel.currentIndex] =
                data?.getBooleanExtra(EXTRA_ANSWER_SHOWN, false) ?: false
        }
    }
}
```

<h2 align = "center">файл CheatActivity</h2>

```kotlin
package com.example.secondapp

import android.app.Activity
import android.content.Context
import android.content.Intent
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import android.widget.Button
import android.widget.TextView

private const val TAG = "CheatActivity"
private const val KEY_INDEX = "answer"

const val EXTRA_ANSWER_SHOWN = "com.bignerdranch.android.secondapp.answer_shown"
private const val EXTRA_ANSWER_IS_TRUE =
    "com.bignerdranch.android.secondapp.answer_is_true"

class CheatActivity : AppCompatActivity() {

    private lateinit var answerTextView: TextView

    private lateinit var showAnswerButton: Button

    private var answerIsTrue = false

    private var shown = false

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_cheat)

        shown = savedInstanceState?.getBoolean(KEY_INDEX, false) ?: false
        setAnswerShownResult(shown)

        answerIsTrue = intent.getBooleanExtra(EXTRA_ANSWER_IS_TRUE, false)
        answerTextView = findViewById(R.id.answer_text_view)
        showAnswerButton = findViewById(R.id.show_answer_button)
        showAnswerButton.setOnClickListener {
            val answerText = when{
                answerIsTrue -> R.string.true_button
                else -> R.string.false_button
            }
            shown = true
            answerTextView.setText(answerText)
            setAnswerShownResult(shown)
        }
    }

    override fun onSaveInstanceState(savedInstanceState: Bundle) {
        super.onSaveInstanceState(savedInstanceState)
        Log.i(TAG, "onSaveInstanceState")
        savedInstanceState.putBoolean(KEY_INDEX, shown)
    }

    private fun setAnswerShownResult(isAnswerShown: Boolean){
        val data = Intent().apply {
            putExtra(EXTRA_ANSWER_SHOWN, isAnswerShown)
        }

        setResult(Activity.RESULT_OK, data)
    }


    companion object {
        fun newIntent(packageContext: Context, answerIsTrue: Boolean) : Intent {
            return Intent(packageContext, CheatActivity::class.java).apply {
                putExtra(EXTRA_ANSWER_IS_TRUE, answerIsTrue)
            }
        }
    }
}
```

<h2 align = "center">файл strings.xml</h2>

```xml
<resources>
    <string name="app_name">SecondApp</string>
    <string name="true_button">True</string>
    <string name="false_button">False</string>
    <string name="question_oceans">The Pacific Ocean is larger than the Atlantic Ocean.</string>
    <string name="question_mideast">The Suez Canal connects the Red Sea and the Indian Ocean.</string>
    <string name="question_africa">The source of the Nile River is in Egypt.</string>
    <string name="question_america">The Amazon River is the longest river in the Americas</string>
    <string name="question_asia">Lake Baikal is tge world\'s oldest and deepest freshwater lake.</string>
    <string name="question_australia">Canberra is the capital of Australia.</string>
    <string name="warning_text">Are you sure you want to do this?</string>
    <string name="show_answer_button">Show Answer</string>
    <string name="cheat_button">Cheat!</string>
    <string name="judgment_toast">Cheating is wrong.</string>
    <string name="correct_toast">Correct!</string>
    <string name="incorrect_toast">Incorrect</string>
</resources>
```

<h2 align = "center">файл QuizViewModel</h2>

```kotlin
package com.example.secondapp

import androidx.lifecycle.ViewModel

class QuizViewModel : ViewModel(){

    private val questionBank = listOf(Questions(R.string.question_australia, true),
        Questions(R.string.question_oceans, true),
        Questions(R.string.question_mideast, false),
        Questions(R.string.question_africa, false),
        Questions(R.string.question_asia, true),
        Questions(R.string.question_america, true)
    )

    var currentIndex = 0

    val isAnswered = IntArray(questionBank.size)

    val isCheated = BooleanArray(questionBank.size)

    val correctAnswer = BooleanArray(questionBank.size)

    val currentQuestionAnswer: Boolean
        get() = questionBank[currentIndex].answer

    val currentQuestionText: Int
        get() = questionBank[currentIndex].textResId

    val currentQuestionSize: Int
        get() = questionBank.size

    fun moveToNext() {
        if(currentIndex == questionBank.size - 1)
        {
            //do nothing
        }
        else {
            currentIndex = (currentIndex + 1) % questionBank.size
        }
    }

    fun moveToPrev() {
        if (currentIndex == 0){
            //do nothing
        }
        else {
            currentIndex = (currentIndex - 1) % questionBank.size
        }
    }

}
```

<h2 align = "center">Codewars</h2>

<h2 align = "center"><a href="https://www.codewars.com/users/akihabara">Аккаунт на codewars</a></h2>

<h2 align = "center">Simple multiplication</h2>

```kotlin
fun simpleMultiplication(n: Int): Int {
    if (n%2==0){
      return n*8  
    } else{
      return n*9
    }
}
```

<h2 align = "center">Remove String Spaces</h2>

```kotlin
fun noSpace(x: String): String {
  return x.replace(" ","")
}
```

<h2 align = "center">String repeat</h2>

```kotlin
fun repeatStr(r: Int, str: String) : String = str.repeat(r)
```

<h2 align = "center">Function 1 - hello world</h2>

```kotlin
fun greet(): String = "hello world!"
```

<h2 align = "center">Square(n) Sum</h2>

```kotlin
package solution

fun squareSum(n: Array<Int>): Int {
    var res : Int = 0
    for(i in n){
        res += (Math.pow(i.toDouble(), 2.0)).toInt()
    }
    return res;
}
```

<h2 align = "center">Century From Year</h2>

```kotlin
fun century(number: Int): Int = if (number % 100 == 0) (number / 100) else ((number / 100) + 1)
```

<h2 align = "center">Is n divisible by x and y?</h2>

```kotlin
fun isDivisible(n: Int, x: Int, y: Int): Boolean = if (n % x == 0 && n % y == 0) true else false
```

<h2 align = "center">Even or Odd</h2>

```Kotlin
fun evenOrOdd(number: Int): String = if (number % 2 == 0) "Even" else "Odd"
```


<h1 align = "center">Вывод</h1>
<p>По итогу проделанной лабораторной работы, я создал новую activity, научился передавать информацию между двумя Activity, прорешал ряд задач на платформе Codewars. </p>