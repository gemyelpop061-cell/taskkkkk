using System;
using System.Collections.Generic;

enum QuestionLevel
{
    Easy = 1,
    Medium = 2,
    Hard = 3
}

abstract class Question
{
    public string Header { get; set; }
    public int Marks { get; set; }
    public QuestionLevel Level { get; set; }

    public virtual void Display()
    {
        Console.WriteLine("[Marks: " + Marks + "] [" + Level + "] " + Header);
    }

    public abstract bool CheckAnswer(string userAnswer);
}

class TrueFalseQuestion : Question
{
    public int CorrectAnswer { get; set; }

    public override void Display()
    {
        base.Display();
        Console.WriteLine("1. True");
        Console.WriteLine("2. False");
    }

    public override bool CheckAnswer(string userAnswer)
    {
        int userAnsNum = Convert.ToInt32(userAnswer);
        return userAnsNum == CorrectAnswer;
    }
}

class ChooseOneQuestion : Question
{
    public string[] Choices = new string[4];
    public int CorrectChoiceIndex { get; set; }

    public override void Display()
    {
        base.Display();
        for (int i = 0; i < 4; i++)
        {
            Console.WriteLine((i + 1) + ". " + Choices[i]);
        }
    }

    public override bool CheckAnswer(string userAnswer)
    {
        int userAnsNum = Convert.ToInt32(userAnswer);
        return userAnsNum == CorrectChoiceIndex;
    }
}

class MultipleChoiceQuestion : Question
{
    public string[] Choices = new string[4];
    public string CorrectAnswersText { get; set; }

    public override void Display()
    {
        base.Display();
        Console.WriteLine("(Select multiple choices separated by comma, e.g. 1,3)");
        for (int i = 0; i < 4; i++)
        {
            Console.WriteLine((i + 1) + ". " + Choices[i]);
        }
    }

    public override bool CheckAnswer(string userAnswer)
    {
        return userAnswer == CorrectAnswersText;
    }
}

class Program
{
    static List<Question> questionBank = new List<Question>();

    static void Main(string[] args)
    {
        while (true)
        {
            Console.Clear();
            Console.WriteLine("=== Examination System ===");
            Console.WriteLine("1. Doctor Mode");
            Console.WriteLine("2. Student Mode");
            Console.WriteLine("3. Exit");
            Console.Write("Enter your choice: ");

            string choice = Console.ReadLine();

            if (choice == "1")
            {
                DoctorMode();
            }
            else if (choice == "2")
            {
                StudentMode();
            }
            else if (choice == "3")
            {
                break;
            }
        }
    }

    static void DoctorMode()
    {
        Console.Clear();
        Console.WriteLine("--- Doctor Mode ---");
        Console.Write("Enter number of questions to add: ");
        int count = Convert.ToInt32(Console.ReadLine());

        for (int i = 0; i < count; i++)
        {
            Console.WriteLine("Adding Question #" + (i + 1) + ":");
            Console.WriteLine("Select Type: 1. True/False | 2. Choose One | 3. Multiple Choice");
            string typeChoice = Console.ReadLine();

            Console.WriteLine("Select Level: 1. Easy | 2. Medium | 3. Hard");
            int levelChoice = Convert.ToInt32(Console.ReadLine());
            QuestionLevel level = QuestionLevel.Easy;
            if (levelChoice == 2) level = QuestionLevel.Medium;
            else if (levelChoice == 3) level = QuestionLevel.Hard;

            Console.Write("Enter question header: ");
            string header = Console.ReadLine();

            Console.Write("Enter marks: ");
            int marks = Convert.ToInt32(Console.ReadLine());

            if (typeChoice == "1")
            {
                TrueFalseQuestion tfq = new TrueFalseQuestion();
                tfq.Header = header;
                tfq.Marks = marks;
                tfq.Level = level;

                Console.Write("Enter correct answer (1 for True, 2 for False): ");
                tfq.CorrectAnswer = Convert.ToInt32(Console.ReadLine());

                questionBank.Add(tfq);
            }
            else if (typeChoice == "2")
            {
                ChooseOneQuestion coq = new ChooseOneQuestion();
                coq.Header = header;
                coq.Marks = marks;
                coq.Level = level;

                for (int c = 0; c < 4; c++)
                {
                    Console.Write("Enter choice " + (c + 1) + ": ");
                    coq.Choices[c] = Console.ReadLine();
                }

                Console.Write("Enter correct choice number (1-4): ");
                coq.CorrectChoiceIndex = Convert.ToInt32(Console.ReadLine());

                questionBank.Add(coq);
            }
            else if (typeChoice == "3")
            {
                MultipleChoiceQuestion mcq = new MultipleChoiceQuestion();
                mcq.Header = header;
                mcq.Marks = marks;
                mcq.Level = level;

                for (int c = 0; c < 4; c++)
                {
                    Console.Write("Enter choice " + (c + 1) + ": ");
                    mcq.Choices[c] = Console.ReadLine();
                }

                Console.Write("Enter correct choices numbers separated by comma (e.g. 1,3): ");
                mcq.CorrectAnswersText = Console.ReadLine();

                questionBank.Add(mcq);
            }
        }

        Console.WriteLine("Questions added successfully! Press any key to return.");
        Console.ReadKey();
    }

    static void StudentMode()
    {
        Console.Clear();
        Console.WriteLine("--- Student Mode ---");
        Console.WriteLine("Select Exam Type: 1. Practical Exam | 2. Final Exam");
        string examType = Console.ReadLine();

        Console.WriteLine("Select Level: 1. Easy | 2. Medium | 3. Hard");
        int levelChoice = Convert.ToInt32(Console.ReadLine());
        QuestionLevel level = QuestionLevel.Easy;
        if (levelChoice == 2) level = QuestionLevel.Medium;
        else if (levelChoice == 3) level = QuestionLevel.Hard;

        List<Question> filteredQuestions = new List<Question>();
        for (int i = 0; i < questionBank.Count; i++)
        {
            if (questionBank[i].Level == level)
            {
                filteredQuestions.Add(questionBank[i]);
            }
        }

        if (filteredQuestions.Count == 0)
        {
            Console.WriteLine("No questions available for this level! Press any key.");
            Console.ReadKey();
            return;
        }

        if (examType == "1")
        {
            int halfCount = filteredQuestions.Count / 2;
            if (halfCount < 1) halfCount = 1;
            List<Question> practicalList = new List<Question>();
            for (int i = 0; i < halfCount; i++)
            {
                practicalList.Add(filteredQuestions[i]);
            }
            filteredQuestions = practicalList;
        }

        int totalPossibleMarks = 0;
        int obtainedMarks = 0;

        for (int i = 0; i < filteredQuestions.Count; i++)
        {
            Question q = filteredQuestions[i];
            totalPossibleMarks += q.Marks;

            Console.Clear();
            q.Display();
            Console.Write("Your Answer: ");
            string userAnswer = Console.ReadLine();

            if (q.CheckAnswer(userAnswer))
            {
                obtainedMarks += q.Marks;
            }
        }

        Console.Clear();
        Console.WriteLine("=== Exam Finished ===");
        Console.WriteLine("Your Result: " + obtainedMarks + " / " + totalPossibleMarks);
        Console.WriteLine("Press any key to return to main menu.");
        Console.ReadKey();
    }
}
