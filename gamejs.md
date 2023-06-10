# Kodları yorumluyorum

### JavaScript gibi çok fazla detay gerektiren yazılım dillerinde çalışırken bu tarz notlar tutmak, yaptığım projeyi derinlemesine anlamamı sağlıyor.
### Bu sefer ki notlarımı sizlerle bir md dosyası şeklinde paylaşmak istedim. Umarım seversinizz :)

<br><br><br>

## 1. Kod Bloğu

``` 
const question = document.getElementById("question");
const choices = Array.from(document.getElementsByClassName("choice-text"));
const progressText = document.getElementById("progressText");
const scoreText = document.getElementById("score");
const progressBarFull = document.getElementById("progressBarFull");
const loader = document.getElementById("loader");
const game = document.getElementById("game"); 
```
- HTML sayfasındaki "question" id'li öğeyi temsil eden bir değişken oluşturulur.
- HTML sayfasındaki "choice-text" sınıfına sahip tüm öğeleri bir diziye dönüştüren bir değişken oluşturulur.
- HTML sayfasındaki "progressText" id'li öğeyi temsil eden bir değişken oluşturulur.
- HTML sayfasındaki "score" id'li öğeyi temsil eden bir değişken oluşturulur.
- HTML sayfasındaki "progressBarFull" id'li öğeyi temsil eden bir değişken oluşturulur.
- HTML sayfasındaki "game" id'li öğeyi temsil eden bir değişken oluşturulur.
- HTML sayfasındaki "loader" id'li öğeyi temsil eden bir değişken oluşturulur.

<br><br><br>

## 2. Kod Bloğu

```
let currentQuestion = {}; 
let acceptingAnswers = false; 
let score = 0; 
let questionCounter = 0; 
let availableQuestions = []; 
let questions = []; 
```

- Geçerli soruyu temsil eden boş bir nesne oluşturulur.
- Cevapların kabul edilip edilmediğini belirten bir bayrak değişkeni oluşturulur.
- Oyuncunun puanını tutan bir değişken oluşturulur.
- Oyuncunun kaçıncı soruda olduğunu tutan bir değişken oluşturulur.
- Kullanılabilir soruları tutan bir dizi oluşturulur.
- Tüm soruları tutan bir dizi oluşturulur.

<br><br><br>

## 3. Kod Bloğu

```
fetch(
  "https://opentdb.com/api.php?amount=20&category=18&difficulty=easy&type=multiple"
)
  .then((res) => {
    return res.json();
  })
  .then((loadedQuestions) => {
    console.log(loadedQuestions.results);
    questions = loadedQuestions.results.map((loadedQuestion) => {
      const formattedQuestion = {
        question: loadedQuestion.question,
      };

      const answerChoices = [...loadedQuestion.incorrect_answers];
      formattedQuestion.answer = Math.floor(Math.random() * 3) + 1;
      answerChoices.splice(
        formattedQuestion.answer - 1,
        0,
        loadedQuestion.correct_answer
      );

      answerChoices.forEach((choice, index) => {
        formattedQuestion["choice" + (index + 1)] = choice;
      });

      return formattedQuestion;
    });
    startGame();
  })
  .catch((err) => {
    console.error(err);
  });
  ```

 - ``Open Trivia Database API``'sından quiz sorularını almak için bir ``GET`` isteği yapılır.
 - API yanıtı alındıktan sonra ``JSON`` formatına dönüştürülür.
 - Yüklenen sorulara dayanarak oyunu başlatmak için bir dizi oluşturulur ve `startGame` işlevi çağrılır.
 - Herhangi bir hata durumunda hata konsoluna mesaj yazdırılır.

<br><br><br>

 ## 4. Kod Bloğu

 ```
const CORRECT_BONUS = 10;
const MAX_QUESTIONS = 3;

startGame = () => {
  questionCounter = 0;
  score = 0;
  availableQuestions = [...questions];
  getNewQuestion();
  game.classList.remove("hidden");
  loader.classList.add("hidden");
};

getNewQuestion = () => {
  if (availableQuestions.length === 0 || questionCounter >= MAX_QUESTIONS) {
    localStorage.setItem("mostRecentScore", score);
    return window.location.assign("/end.html");
    //go to the end page
  }
  questionCounter++;
  progressText.innerText = ` Question ${questionCounter}/${MAX_QUESTIONS}`;
  //update the progress bar
  progressBarFull.style.width = `${(questionCounter / MAX_QUESTIONS) * 100}%`;

  const questionIndex = Math.floor(Math.random() * availableQuestions.length);
  currentQuestion = availableQuestions[questionIndex];
  question.innerText = currentQuestion.question;

  choices.forEach((choice) => {
    const number = choice.dataset["number"];
    choice.innerText = currentQuestion["choice" + number];
  });

  availableQuestions.splice(questionIndex, 1);
  acceptingAnswers = true;
};
 ```

- Doğru cevap verildiğinde oyuncuya verilecek bonus puanı belirleyen bir sabit oluşturulur.
- Oyunda toplam soru sayısını belirleyen bir sabit oluşturulur.
- Oyunu başlatan işlev tanımlanır.
- ``questionCounter`` ve ``score`` sıfırlanır.
- ``availableQuestions`` dizisi, ``questions`` dizisinin bir kopyası olarak ayarlanır.
- ``getNewQuestion`` işlevi çağrılarak yeni bir soru alınır.
- HTML sayfasında oyun alanını göstermek için ``game`` öğesine "hidden" sınıfı kaldırılır.
- HTML sayfasında yüklenme ekranını gizlemek için loader öğesine "hidden" sınıfı eklenir.
- ``getNewQuestion = () => { ... }`` - Yeni bir soru almak için işlev tanımlanır.
- Eğer ``availableQuestions`` dizisi boşsa veya ``questionCounter`` ``MAX_QUESTIONS`` değerine ulaştıysa, oyun tamamlanmış demektir. Oyuncunun en son puanı ``localStorage``'a kaydedilir ve end.html sayfasına yönlendirilir.
- Soru sayacı artırılır.
- HTML sayfasındaki ilerleme metnini güncellemek için ``progressText`` öğesi kullanılır.
- İlerleme çubuğunu güncellemek için ``progressBarFull`` öğesinin genişliği ayarlanır.
- ``availableQuestions`` dizisinden rastgele bir soru seçilir ve ``currentQuestion`` değişkenine atanır.
- Soruyu HTML sayfasındaki ``question`` öğesine yerleştirilir.
- Seçenekler için choices dizisindeki her bir öğe üzerinde döngü yapılır. Seçenek metinleri ``currentQuestion`` nesnesinden alınarak ilgili öğeye yerleştirilir.
- Kullanılan soruyu ``availableQuestions`` dizisinden kaldırarak tekrar seçilmemesi sağlanır.
- Cevapların kabul edilmesi aktifleştirilir.

<br><br><br>

 ## 5. Kod Bloğu

 ```
choices.forEach((choice) => {
  choice.addEventListener("click", (e) => {
    if (!acceptingAnswers) return;

    acceptingAnswers = false;
    const selectedChoice = e.target;
    const selectedAnswer = selectedChoice.dataset["number"];

    const classToApply =
      selectedAnswer == currentQuestion.answer ? "correct" : "incorrect";

    if (classToApply === "correct") {
      incrementScore(CORRECT_BONUS);
    }

    selectedChoice.parentElement.classList.add(classToApply);

    setTimeout(() => {
      selectedChoice.parentElement.classList.remove(classToApply);
      getNewQuestion();
    }, 1000);
  });
});

incrementScore = (num) => {
  score += num;
  scoreText.innerText = score;
};
 ```

- Her seçeneğin tıklanma olayını dinleyen bir döngü oluşturulur.
- Eğer cevaplar kabul edilmiyorsa (``acceptingAnswers`` false ise), işlevden çıkılır.
- Cevaplar kabul edilebilir durumdaysa, seçilen seçeneği ve ilgili cevabı alır.
- Seçilen cevabın doğru olup olmadığına göre ``classToApply`` değişkenine "correct" veya "incorrect" sınıfı atanır.
- Eğer ``classToApply`` "correct" ise, puanı ``CORRECT_BONUS`` kadar artıran ``incrementScore`` işlevi çağrılır.
- Seçilen seçeneğin ebeveyn öğesine ``classToApply`` sınıfı eklenir, böylece doğru veya yanlış cevap için uygun stil uygulanır.
- 1 saniye sonra, seçilen seçeneğin ebeveyn öğesinden ``classToApply`` sınıfı kaldırılır ve bir sonraki soruya geçmek için ``getNewQuestion`` işlevi çağrılır.
- ``incrementScore = (num) => { ... }`` - Puanı artıran bir işlev tanımlanır.
- ``num`` parametresi kadar puan eklenir.
- Puan metnini güncellemek için ``scoreText`` öğesi kullanılır.

<br><br><br>

Bu kodlar, HTML sayfasında bir quiz oyununun çalışmasını sağlıyor. Sorular Open Trivia Database API'sından alınır ve kullanıcıların sorulara yanıt vermesi ve puan kazanması sağlanır.

Okuduğunuz için teşekkür ederim. 


