# Quize_game
quiz_game
CREATE TABLE questions (
    id INT AUTO_INCREMENT PRIMARY KEY,
    question TEXT,
    option1 VARCHAR(255),
    option2 VARCHAR(255),
    option3 VARCHAR(255),
    option4 VARCHAR(255),
    correct_option INT
);
CREATE TABLE results (
    id INT AUTO_INCREMENT PRIMARY KEY,
    player_name VARCHAR(100),
    score INT,
    played_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
Start Game
   ↓
Enter Player Name
   ↓
Fetch 5 Random Questions
   ↓
Timer Starts (10 sec/question)
   ↓
Answer Question
   ↓
Score Calculation
   ↓
Save Result to DB
   ↓
Restart / Exit
JavaFX UI Components

Label → Question

RadioButtons → Options

Button → Next

Button → Restart

Button → Exit

Label → Timer

Label → Score
public class DBConnection {
    private static final String URL = "jdbc:mysql://localhost:3306/quiz_game";
    private static final String USER = "root";
    private static final String PASS = "";

    public static Connection getConnection() throws Exception {
        return DriverManager.getConnection(URL, USER, PASS);
    }
}
public class QuestionDAO {

    public static List<Question> getRandomQuestions() throws Exception {
        List<Question> list = new ArrayList<>();
        Connection con = DBConnection.getConnection();
        String sql = "SELECT * FROM questions ORDER BY RAND() LIMIT 5";
        PreparedStatement ps = con.prepareStatement(sql);
        ResultSet rs = ps.executeQuery();

        while (rs.next()) {
            list.add(new Question(
                rs.getInt("id"),
                rs.getString("question"),
                rs.getString("option1"),
                rs.getString("option2"),
                rs.getString("option3"),
                rs.getString("option4"),
                rs.getInt("correct_option")
            ));
        }
        return list;
    }
}
public class Question {
    int id, correct;
    String q, o1, o2, o3, o4;

    public Question(int id, String q, String o1, String o2, String o3, String o4, int correct) {
        this.id = id;
        this.q = q;
        this.o1 = o1;
        this.o2 = o2;
        this.o3 = o3;
        this.o4 = o4;
        this.correct = correct;
    }
}
private IntegerProperty timeSeconds = new SimpleIntegerProperty(10);
Timeline timeline;

private void startTimer() {
    timeSeconds.set(10);
    timeline = new Timeline(
        new KeyFrame(Duration.seconds(1), e -> {
            timeSeconds.set(timeSeconds.get() - 1);
            if (timeSeconds.get() <= 0) {
                nextQuestion();
            }
        })
    );
    timeline.setCycleCount(10);
    timeline.play();
}
if (selectedOption == currentQuestion.correct) {
    score += 10;
}
public static void saveResult(String name, int score) throws Exception {
    Connection con = DBConnection.getConnection();
    String sql = "INSERT INTO results(player_name, score) VALUES (?,?)";
    PreparedStatement ps = con.prepareStatement(sql);
    ps.setString(1, name);
    ps.setInt(2, score);
    ps.executeUpdate();
}
exitBtn.setOnAction(e -> System.exit(0));
restartBtn.setOnAction(e -> {
    score = 0;
    questionIndex = 0;
    loadQuestions();
});
