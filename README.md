import java.util.Scanner;
import java.util.Timer;
import java.util.TimerTask;

class Question {
    String questionText;
    String[] options;
    int correctOption;

    public Question(String questionText, String[] options, int correctOption) {
        this.questionText = questionText;
        this.options = options;
        this.correctOption = correctOption;
    }
}

public class QuizApp {
    private Question[] questions;
    private int score = 0;
    private int currentQuestionIndex = 0;
    private boolean answerSubmitted = false;

    public QuizApp(Question[] questions) {
        this.questions = questions;
    }

    public void startQuiz() {
        Scanner scanner = new Scanner(System.in);
        Timer timer = new Timer();

        for (Question question : questions) {
            answerSubmitted = false;
            System.out.println("Question " + (currentQuestionIndex + 1) + ": " + question.questionText);
            for (int i = 0; i < question.options.length; i++) {
                System.out.println((i + 1) + ". " + question.options[i]);
            }

            TimerTask task = new TimerTask() {
                public void run() {
                    if (!answerSubmitted) {
                        System.out.println("\nTime's up!");
                        checkAnswer(-1); // Automatically wrong if time's up
                        proceedToNextQuestion();
                    }
                }
            };

            timer.schedule(task, 10000); // Set timer to 10 seconds for each question

            System.out.print("Your answer (1-" + question.options.length + "): ");
            int userAnswer = scanner.nextInt();
            answerSubmitted = true;
            task.cancel();

            checkAnswer(userAnswer - 1);
            proceedToNextQuestion();
        }

        displayResults();
    }

    private void checkAnswer(int userAnswer) {
        if (userAnswer == questions[currentQuestionIndex].correctOption) {
            System.out.println("Correct!\n");
            score++;
        } else {
            System.out.println("Incorrect. The correct answer was: " + (questions[currentQuestionIndex].correctOption + 1) + ". " + questions[currentQuestionIndex].options[questions[currentQuestionIndex].correctOption] + "\n");
        }
    }

    private void proceedToNextQuestion() {
        currentQuestionIndex++;
        if (currentQuestionIndex >= questions.length) {
            displayResults();
            System.exit(0);
        }
    }

    private void displayResults() {
        System.out.println("Quiz Over!");
        System.out.println("Your final score is: " + score + " out of " + questions.length);
        System.out.println("Summary of answers:");
        for (int i = 0; i < questions.length; i++) {
            System.out.println("Question " + (i + 1) + ": " + questions[i].questionText);
            System.out.println("Correct Answer: " + (questions[i].correctOption + 1) + ". " + questions[i].options[questions[i].correctOption]);
            if (i < currentQuestionIndex) {
                System.out.println("Your Answer: " + ((questions[i].correctOption == i) ? "Correct" : "Incorrect"));
            }
            System.out.println();
        }
    }

    public static void main(String[] args) {
        Question[] questions = new Question[]{
            new Question("What is the capital of France?", new String[]{"Berlin", "Madrid", "Paris", "Rome"}, 2),
            new Question("What is 5 + 7?", new String[]{"10", "11", "12", "13"}, 2),
            new Question("Who wrote 'To Kill a Mockingbird'?", new String[]{"Harper Lee", "Mark Twain", "Ernest Hemingway", "F. Scott Fitzgerald"}, 0),
        };

        QuizApp quizApp = new QuizApp(questions);
        quizApp.startQuiz();
    }
}
