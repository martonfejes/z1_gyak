2024 recipebook

namespace Common
{
    public interface IFollowable
    {
        void AddNext(IFollowable next);
        IFollowable GetNext();
    }
}

namespace Common
{
    public class Instruction : IFollowable
    {
        private IFollowable nextInstruciton;
        public void AddNext(IFollowable next)
        { 
            nextInstruciton = next;
        }
        public IFollowable GetNext()
        {
            return nextInstruciton;
        }
    }
}

namespace Common
{
    public class RecipeBook
    {
        public IFollowable firstInstruction;
        public IEnumerable<IFollowable> GetInstrucitons()
        {
            var currentInstruciton = firstInstruction;
            while (currentInstruciton != null)
            {
                yield return currentInstruciton;
                currentInstruciton = currentInstruciton.GetNext();
            }
        }

        public int CountWhere(string searchTerm)
        {
            return GetInstrucitons().Count(instruction => instruction.ToString() == searchTerm);
        }
    }
}

namespace Common
{
    public class Step : Instruction
    {
        public string Description { get; }
        public Step(string description)
        { 
            Description = description;
        }

        public override string ToString()
        { 
            return Description;
        }
    }
}

namespace ConsoleApplication
{
    public class Program
    {
        static void Main()
        { 
            var recipeBook = new RecipeBook();

            var step1 = new Step("Liszt hozzáadása");
            var step2 = new Step("Tojás hozzáadása");
            var step3 = new Step("Cukor hozzáadása");
            var step4 = new Step("Keverés");
            var step5 = new Step("Sütés");

            recipeBook.firstInstruction = step1;
            step1.AddNext(step2);
            step2.AddNext(step3);
            step3.AddNext(step4);
            step4.AddNext(step5);

            foreach (var instruction in recipeBook.GetInstrucitons())
            {
                Console.WriteLine(instruction.ToString());
            }
        }
    }
}

namespace Tests
{
    public class RecipeBookTest
    {
        [Fact]
        public void TestCount()
        {
            var instruction1 = new Instruction();
            var instruction2 = new Instruction();
            var instruction3 = new Instruction();

            var recipeBook = new RecipeBook();
            recipeBook.firstInstruction = instruction1;

            instruction1.AddNext(instruction2);
            instruction2.AddNext(instruction3);

            var steps = recipeBook.GetInstrucitons();
            Assert.Equal(3, steps.Count());

        }

        [Fact]
        public void TestStepCount()
        { 
            var recipebook = new RecipeBook();
            var step1 = new Step("Keverés");
            var step2 = new Step("Hozzáadás");
            var step3 = new Step("Keverés");
            var step4 = new Step("Hozzáadás");
            var step5 = new Step("Keverés");
            var step6 = new Step("Sütés");

            recipebook.firstInstruction = step1;
            step1.AddNext(step2);
            step2.AddNext(step3);
            step3.AddNext(step4);
            step4.AddNext(step5);
            step5.AddNext(step6);

            int count = recipebook.CountWhere("Keverés");
            Assert.Equal(3, count); 

        }
    }
}
---------------------------------------------------------------------------------------------------------------------
2023_A

namespace Common
{
    public class Person
    {
        public string Name { get; set; }
        public int Age { get; set; }
        public int Height { get; set; }

        public static Person GetMe()
        {
            return new Person {Name = "Marci", Age= 22, Height = 185 };
        }

        public override string ToString()
        {
            return $"{Name}, {Age} éves, {Height} cm magas";
        }
    }
}

namespace Common
{
    public interface IPersonSource
    {
        IEnumerable<Person> GetPersons();
    }
}

namespace Common
{
    public class TrivialPersonSource : IPersonSource
    {
        public int n;

        public TrivialPersonSource(int N)
        {
            n = N;  
        }

        public IEnumerable<Person> GetPersons()
        {
            for(int i = 0; i < n; i++)
            {
                yield return Person.GetMe();
            }
        }
    }
}

namespace Common
{
    public class AdvancedPersonSource : IPersonSource
    {
        public List<Person> persons = new List<Person>();

        public IEnumerable<Person> GetPersons()
        {
            foreach (Person person in persons)
            {
                yield return person;
            }
        }

        public void Add(string name, int age, int height)
        {
            persons.Add(new Person
            {
                Name = name,
                Age = age,
                Height = height
            });
        }
    }
}

namespace ConsoleApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var person1 = new TrivialPersonSource(3);
            var person2 = new AdvancedPersonSource();

            foreach(var person in person1.GetPersons())
            {
                Console.WriteLine(person);
            }

            person2.Add("Zoli", 22, 186);
            person2.Add("Jani", 20, 140);
            person2.Add("Győző", 12, 112);

            Console.WriteLine();

            foreach(var person in person2.GetPersons())
            {
                Console.WriteLine(person);
            }
        }
    }
}
inplicit modon kiiratás az override miatt nem kell tostring()

namespace Tests
{
    public class AdvancedPersonSourceTests
    {
        [Fact]
        public void Test()
        {
            var person = new AdvancedPersonSource();

            person.Add("Zoli", 22, 186);
            person.Add("Jani", 20, 140);
            person.Add("Győző", 12, 112);

            Assert.Equal(3, person.GetPersons().Count());

        }
    }
}

namespace Tests
{
    public class TrivialPersonSourceTests
    {
        [Fact]
        public void TestCount()
        {
            var person = new TrivialPersonSource(12);

            Assert.Equal(12, person.GetPersons().Count());
        }
    }
}
-----------------------------------------------------------------------------------------------------------------
2022_A

namespace Common
{
    public class Person
    {
        public string Name { get; set; }
        public int Age { get; set; }
        public int Height { get; set; }

        public Person(string name, int age, int height)
        {
            Name = name;
            Age = age;
            Height = height;
        }

        public static Person GetMe()
        {
            return new Person("Marci", 22, 185);
        }

        public string GetDescription()
        {
            int birthYear = DateTime.Now.Year - Age;
            return $"{Name} ({Height} cm) születésnapja {birthYear}";
        }
    }
}

namespace Common
{
    public interface IPersonModifier
    {
     void Modify(Person person);
    }
}

namespace Common
{
    public class PersonGrower : IPersonModifier
    {
        public int N { get; }

        public PersonGrower(int n) 
        {
            N = n;
        }

        public void Modify(Person person)
        {
            person.Height += N;
        }
    }
}

namespace Common
{
    public class ClassRoom
    {
        private List<Person> persons = new List<Person>();

        public void Add(Person person)
        {
            persons.Add(person);
        }

        public int Count()
        {
            return persons.Count;
        }

        public double GetAverageAge()
        {
          if (persons.Count == 0) { return 0; }
          return persons.Average(p => p.Age);
        }

        public Person GetTallestPerson()
        {
            if (persons.Count == 0) { return null; }
            return persons.MaxBy(p => p.Height);
        }
    }
}

using Common;
namespace ConsoleApplication
{
    public class Program
    {
        public static void Main(string[] args) 
        { 
            var grower = new PersonGrower(5);

            PrintAndModify(grower);

        }

        public static void PrintAndModify(IPersonModifier modifier)
        {
            var p = Person.GetMe();

            Console.WriteLine(p.GetDescription());

            for (int i = 0; i < 10; i++)
            {
                modifier.Modify(p);
                Console.WriteLine(p.GetDescription());
            }
        }
    }

}

using Common;
namespace Tests;

public class ClassRoomTests
{
    [Fact]
    public void TestAddition()
    {
        var room = new ClassRoom();

        room.Add(Person.GetMe());
        room.Add(Person.GetMe());
        room.Add(Person.GetMe());

        Assert.Equal(3, room.Count());
    }

    [Fact]
    public void TestHeightInClass()
    {
        var room = new ClassRoom();
        var modify = new PersonGrower(10);

        var person1 = new Person("Zoli", 20, 185);
        var person2 = new Person("Béci", 22, 189);
        var person3 = new Person("Attila", 19, 190);
        var person4 = new Person("Gyuri", 22, 175);

        room.Add(person1);
        room.Add(person2);
        room.Add(person3);
        room.Add(person4);

        Person tallestBefore = room.GetTallestPerson();
        Assert.Equal(person3, tallestBefore);


        modify.Modify(person2);
        Assert.Equal(199, person2.Height);

        Person tallestAfter = room.GetTallestPerson();
        Assert.Same(person2, tallestAfter);
        

    }
}

using Common;
namespace Tests;

public class ModifierTests
{
    [Fact]
    public void TestGrowPerson()
    {
        var person = new Person("Zoli", 20, 180);
        var grower = new PersonGrower(5);

        var originalHeight = person.Height;

        grower.Modify(person);

        Assert.Equal(originalHeight + 5, person.Height);
        
    }
}
--------------------------------------------------------------------------------------------------------------------
2022_B

ugyan az mint a másik az insert az elejére rakja be meg kell neki adni az indexet meg hogy mit tegyen be
public void Insert(Animal animal)
{
    animals.Insert(0, animal);
}
--------------------------------------------------------------------------------------------------------------------
2021_ösz

namespace Common
{
    public class SimpleGenerator : IBoolSource
    {
        public bool ValueToGenerate { get;}

        public SimpleGenerator(bool valueToGenerate)
        {
            ValueToGenerate = valueToGenerate;
        }

        public IEnumerable<bool> GetBools(int N)
        {
            for (int i = 0; i < N; i++)
            {
                yield return ValueToGenerate;
            }
        }
    }

}

namespace Common
{
    public class PatternGenerator : IBoolSource
    {
        public IEnumerable<bool>GetBools(int N)
        {
            for (int i = 0; i < N; i++) 
            {
                int r = i % 3;
                yield return r == 0;
            }
        }
    }
}

namespace Common
{
    public interface IBoolSource
    {
        IEnumerable<bool> GetBools(int N);
    }
}

namespace ConsoleApplication
{
    public class Program
    {
         public static void Main(string[] args)
        {
            var generator = new SimpleGenerator(true);

            ShowBools(generator, 5);

            var pattern = new PatternGenerator();
            ShowBools(pattern, 15);

        }

        public static void ShowBools(IBoolSource boolsource, int number)
        {
            int trueCount = boolsource.GetBools(number).Count(b => b);
            Console.WriteLine(trueCount);
        }
    }
}

public class PatternGeneratorTests
{
    [Fact]
    public void BasicTest()
    {
        var pattern = new PatternGenerator();
        var actual = pattern.GetBools(5).ToArray();

        var expected = new[] { true, false, false, true, false };
        Assert.Equal(expected, actual);
    }
}

public class SimpleGenerator
{
    [Fact]
    public void BasicTest()
    {
       var generator = new Common.SimpleGenerator(true);


        Assert.Equal(10, generator.GetBools(10).Count());
    }
}
-------------------------------------------------------------------------------------------------------------------------
2020_A primszámgenerator

namespace Common
{
    public class ZeroGenerator : INumberSequenceSource
    {
        public int N { get;}

        public ZeroGenerator(int n) 
        {
            N = n;
        }

        public IEnumerable<int> GenerateNumbers()
        {
            for(int i = 0; i < N; i++)
            {
                yield return 0;
            }
        }
    }
}

namespace Common
{
    public interface INumberSequenceSource
    {
     IEnumerable<int> GenerateNumbers();
    }
}

namespace Common
{
    public class PrimeGenerator : INumberSequenceSource
    {
        public int N {  get;}
        public PrimeGenerator(int n) 
        {
            N = n;
        }
        public IEnumerable<int> GenerateNumbers()
        {
            for(int found = 0, x = 2; found < N; x++)
            {
                if (IsPrime(x))
                {
                    yield return x; found++;
                }
            }
        }

        private bool IsPrime(int x)
        {
            if (x < 2) return false;
            if(x % 2 == 0) return x == 2;
            for(int d = 3; d*d <= x; d += 2)
            {
                if(x % d == 0) return false;
            }
            return true;
        }
    }
}

namespace ConsoleApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var zero = new ZeroGenerator(5);
            ShowNumbers(zero);

            var primeThree = new PrimeGenerator(100).GenerateNumbers();

            int countEndingWith3 = primeThree.Count(p => p % 10 == 3);
            Console.WriteLine(countEndingWith3);
        }

        public static void ShowNumbers(INumberSequenceSource ins)
        {
            var numbers = ins.GenerateNumbers().ToList();

            for (int i = 0; i < numbers.Count; i++)
            {
                Console.WriteLine($"{i}. elem: {numbers[i]}");
            }
        }
    }
}

public class PrimeTests
{
    [Fact]
    public void FivePrimeTest()
    {
        var  src = new PrimeGenerator(5);
        var expected = new[] { 2, 3, 5, 7, 11};

        var actual = src.GenerateNumbers();

        Assert.Equal(expected, actual);
        
    }
}

namespace Tests;


public class ZeroTests
{
    [Fact]
    public void BasicTest()
    {
        var zerog = new ZeroGenerator(10);

        Assert.Equal(10, zerog.GenerateNumbers().Count());
    }
}
---------------------------------------------------------------------------------------------------------------------------
2020_pót fontos öröklés és regex
namespace Common
{
    public class Student : Person
    {
        public string NeptunCode { get;}

        //öröklés : base(valami, valami)
        public Student(string name, int age, string neptun) : base(name, age)
        {

            var pattern = @"^[A-Z0-9]{6}$";
            var match = Regex.IsMatch(neptun, pattern);
            if (!match)
            {
                throw new ArgumentException("Érvénytelen Neptun-kód: pontosan 6 karakter, csak nagybetű és szám engedélyezett.",
                    nameof(neptun));
            }
            else
            {
                NeptunCode = neptun;
            }
        }
    }
}

namespace Common
{
    public class Person
    {
        public string Name { get;}
        public int Age { get;}

        public Person(string name, int age  )
        {
            Name = name;
            Age = age;
        }

        public override string ToString()
        {
            return $"Hello, I`m {Name} and {Age} years old";
        }
    }
}

namespace ConsoleApplication
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var person = new Person("Marci", 22);
            ShowPersonDetails(person);

            var student = new Student("Zoli", 20, "JE0171");
            ShowStudentDetails(student);
        }

        public static void ShowPersonDetails(Person person)
        {
            Console.WriteLine($"{person.Name}, {person.Age}, {person}");
        }

        public static void ShowStudentDetails(Student student)
        {
            ShowPersonDetails(student);
            Console.WriteLine(student.NeptunCode );
        }
    }
}

namespace Tests;

public class PersonTests
{
    [Fact]
    public void PersonToString()
    {
        var person = new Person("Aladár", 22);

        // nevet és az életkort ellenőrzi.
        Assert.Equal("Aladár", person.Name); 
        Assert.Equal(22, person.Age);

        var result = "Hello, I`m Aladár and 22 years old";
        Assert.Equal(result, person.ToString());

    }

    [Fact]
    public void StudentTextException()
    {
        Assert.Throws<ArgumentException>(() => new Student("Cecil", 42, "Cecil.42"));

    }
}

namespace Tests;

public class StudentTests
{
    [Fact]
    public void StudentTestSuccess()
    {
        var student = new Student("Béla", 20, "B1TM4N");

        Assert.Equal("Béla", student.Name);
        Assert.Equal(20, student.Age);
        Assert.Equal("B1TM4N", student.NeptunCode);
    
    }
}
-------------------------------------------------------------------------------------------------------------
