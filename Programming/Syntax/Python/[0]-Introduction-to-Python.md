[⬅ Back to README](../../../README.md)

# Python


Welcome! This is a self-paced course for learning Python, a general-purpose programming language known for its readability and versatility across web development, automation, data science, and more.

---

## What is Python?

Python lets you:
- Write clean, readable code with simple syntax
- Automate repetitive tasks and scripts
- Build web applications, APIs, and command-line tools
- Analyze data and work with libraries for data science and machine learning
- Work with files, databases, and external APIs
- Organize code using functions, classes, and modules
- Communicate over networks with sockets, RPC, and messaging systems
- Secure applications with encryption, hashing, and authentication
- Build GUIs, test and package software, and optimize performance
- Build and deploy production-ready, cloud-native software

## Table of Contents

**Getting Started**  

1. **[Installing Python & First-Time Setup](./[1]-Installation-and-Setup.md)**  
    1.1 What You Need Before You Start  
    1.2 Installing Python on Windows  
    1.3 Installing Python on macOS  
    1.4 Installing Python on Linux  
    1.5 Verifying Your Installation  
    1.6 Choosing a Code Editor / IDE  
2. **[Running Code: Scripts, the REPL & Notebooks](./[2]-Running-Python-Code.md)**  
    2.1 The Python Interpreter  
    2.2 Running Scripts (.py files)  
    2.3 The Interactive REPL  
    2.4 Jupyter Notebooks  
    2.5 Which Should You Use?  
3. **[Virtual Environments & Package Management (pip, venv, poetry, conda)](./[3]-Virtual-Environments-and-Pip.md)**  
    3.1 Why Virtual Environments Matter  
    3.2 venv - Python's Built-in Tool  
    3.3 pip - The Package Installer  
    3.4 requirements.txt  
    3.5 Poetry  
    3.6 Conda  
4. **[Environment Variables & Configuration (.env, python-dotenv, os.environ)](./[4]-Environment-Variables-and-Configuration.md)**  
    4.1 What Are Environment Variables  
    4.2 Reading Env Vars with os.environ  
    4.3 .env files and python-dotenv  
    4.4 Best Practices for Secrets & Config  

**Core Syntax**  

5. **[Variables & Basic Data Types](./[5]-Variables-and-Data-Types.md)**  
    5.1 What is a Variable?  
    5.2 Naming Rules & Conventions  
    5.3 Dynamic Typing  
    5.4 Basic Data Types Overview  
    5.5 Type Checking with type() and isinstance()  
    5.6 Type Casting/Conversion  
6. **[Numbers, Strings & Booleans](./[6]-Numbers-Strings-and-Booleans.md)**  
    6.1 Integers and Floats  
    6.2 Arithmetic with Numbers  
    6.3 Strings: Creation and Basics  
    6.4 String Immutability  
    6.5 Booleans and Truthiness  
7. **[Operators & Expressions (arithmetic, comparison, logical, bitwise, identity, membership)](./[7]-Operators-and-Expressions.md)**  
    7.1 Arithmetic Operators  
    7.2 Comparison Operators  
    7.3 Logical Operators  
    7.4 Assignment Operators  
    7.5 Bitwise Operators  
    7.6 Identity Operators (is, is not)  
    7.7 Membership Operators (in, not in)  
    7.8 Operator Precedence  
8. **[Conditionals: if, elif, else](./[8]-Conditionals.md)**  
    8.1 The if Statement  
    8.2 elif and else  
    8.3 Nested Conditionals  
    8.4 Conditional (Ternary) Expressions  
    8.5 Truthy and Falsy Values in Conditions  
9. **[Loops: for & while, break, continue, pass](./[9]-Loops.md)**  
    9.1 The for Loop  
    9.2 The range() Function  
    9.3 The while Loop  
    9.4 break, continue and pass  
    9.5 The else Clause on Loops  
    9.6 Nested Loops  
10. **[Functions & Scope (*args, **kwargs, lambda, LEGB rule)](./[10]-Functions-and-Scope.md)**  
    10.1 Defining and Calling Functions  
    10.2 Parameters, Arguments & Return Values  
    10.3 Default and Keyword Arguments  
    10.4 *args and **kwargs  
    10.5 Lambda Functions  
    10.6 Scope and the LEGB Rule  
11. **[String Formatting & Manipulation (f-strings, slicing, methods)](./[11]-String-Formatting.md)**  
    11.1 f-strings  
    11.2 The .format() Method  
    11.3 % Formatting (legacy)  
    11.4 String Slicing  
    11.5 Common String Methods  
12. **[Error Handling: try, except, else, finally, custom exceptions](./[12]-Error-Handling.md)**  
    12.1 What Are Exceptions?  
    12.2 try / except  
    12.3 else and finally  
    12.4 Catching Multiple/Specific Exceptions  
    12.5 Raising Exceptions  
    12.6 Custom Exceptions  

**Data Structures**  

13. **[Lists, Tuples & Sets](./[13]-Lists-Tuples-and-Sets.md)**  
    13.1 Lists: Ordered & Mutable  
    13.2 Common List Methods  
    13.3 Tuples: Ordered & Immutable  
    13.4 Why Use Tuples?  
    13.5 Sets: Unordered & Unique  
    13.6 Set Operations  
14. **[Dictionaries](./[14]-Dictionaries.md)**  
    14.1 What is a Dictionary?  
    14.2 Creating and Accessing Dictionaries  
    14.3 Adding, Updating, Removing Items  
    14.4 Iterating Over Dictionaries  
    14.5 Dictionary Methods  
    14.6 Nested Dictionaries  
15. **[Comprehensions: List, Dict & Set](./[15]-Comprehensions.md)**  
    15.1 List Comprehensions  
    15.2 Conditional Logic in Comprehensions  
    15.3 Dict Comprehensions  
    15.4 Set Comprehensions  
    15.5 Nested Comprehensions  
    15.6 When Not to Use Comprehensions  
16. **[Collections Module (Counter, defaultdict, namedtuple, deque, OrderedDict)](./[16]-Collections-Module.md)**  
    16.1 Counter  
    16.2 defaultdict  
    16.3 namedtuple  
    16.4 deque  
    16.5 OrderedDict  

**Object-Oriented Programming**  

17. **[Classes & Objects](./[17]-OOP-Classes-and-Objects.md)**  
    17.1 What is a Class?  
    17.2 Defining a Class and Creating Objects  
    17.3 The __init__ Method and self  
    17.4 Instance Attributes vs Class Attributes  
    17.5 Instance Methods  
18. **[Inheritance & Polymorphism (incl. multiple inheritance, MRO)](./[18]-Inheritance-and-Polymorphism.md)**  
    18.1 What is Inheritance?  
    18.2 Overriding Methods  
    18.3 The super() Function  
    18.4 Multiple Inheritance  
    18.5 Method Resolution Order (MRO)  
    18.6 Polymorphism  
19. **[Encapsulation & Abstraction](./[19]-Encapsulation-and-Abstraction.md)**  
    19.1 What is Encapsulation?  
    19.2 Public, Protected, and Private Attributes  
    19.3 Name Mangling  
    19.4 Abstraction  
20. **[Magic/Dunder Methods (`__str__`, `__eq__`, `__len__`, etc.)](./[20]-Magic-Methods.md)**  
    20.1 What Are Dunder/Magic Methods?  
    20.2 __str__ and __repr__  
    20.3 __eq__ and Other Comparisons  
    20.4 __len__  
    20.5 Other Common Magic Methods  
21. **[Class Methods, Static Methods & Properties](./[21]-Class-and-Static-Methods.md)**  
    21.1 Instance Methods Recap  
    21.2 Class Methods (@classmethod)  
    21.3 Static Methods (@staticmethod)  
    21.4 Properties (@property)  
22. **[Abstract Base Classes (ABC module)](./[22]-Abstract-Base-Classes.md)**  
    22.1 What is an Abstract Base Class?  
    22.2 The abc Module  
    22.3 Defining Abstract Methods  
    22.4 Why Use ABCs?  
23. **[Dataclasses](./[23]-Dataclasses.md)**  
    23.1 The Problem Dataclasses Solve  
    23.2 The @dataclass Decorator  
    23.3 Default Values and Field()  
    23.4 Comparing and Ordering Dataclasses  
    23.5 Immutable Dataclasses (frozen=True)  
24. **[Data Validation with Pydantic](./[24]-Pydantic.md)**  
    24.1 What is Pydantic and Why Use It?  
    24.2 Defining a BaseModel  
    24.3 Validation and Error Handling  
    24.4 Field Constraints and Defaults  
    24.5 Nested Models  

**Advanced Language Features**  
    25. **[Iterators & Generators (yield, iter, next)](./[25]-Iterators-and-Generators.md)**  
    26. **[Decorators](./[26]-Decorators.md)**  
    27. **[Context Managers (`with`, `__enter__`/`__exit__`)](./[27]-Context-Managers.md)**  
    28. **[Closures](./[28]-Closures.md)**  
    29. **[Type Hints & Static Typing (typing module, mypy)](./[29]-Type-Hints.md)**  
    30. **[Pattern Matching (`match`/`case`, structural patterns)](./[30]-Pattern-Matching.md)**  
    31. **[Metaclasses](./[31]-Metaclasses.md)**  
    32. **[Descriptors](./[32]-Descriptors.md)**  
    33. **[Functional Programming (map, filter, reduce, functools, itertools)](./[33]-Functional-Programming.md)**  
    34. **[Memory Management & Garbage Collection](./[34]-Memory-Management.md)**  
    35. **[The Python Interpreter Internals (bytecode, dis module, the GIL)](./[35]-Python-Interpreter-Internals.md)**  

**Standard Library**  
    36. **[Working with Files (open, read/write, pathlib)](./[36]-Working-with-Files.md)**  
    37. **[Modules & Packages](./[37]-Modules-and-Packages.md)**  
    38. **[Working with Dates & Times (datetime, time)](./[38]-Dates-and-Times.md)**  
    39. **[Regular Expressions (re module)](./[39]-Regular-Expressions.md)**  
    40. **[Data Serialization (json, csv, pickle, XML)](./[40]-Data-Serialization.md)**  
    41. **[Logging](./[41]-Logging.md)**  
    42. **[Command-Line Tools (argparse, sys.argv, Click, Typer)](./[42]-CLI-Tools.md)**  
    43. **[System & OS Operations (os, sys, shutil, subprocess)](./[43]-System-and-OS-Operations.md)**  
    44. **[Math, Random & Statistics](./[44]-Math-Random-Statistics.md)**  
    45. **[Internationalization & Localization (gettext, locale, babel)](./[45]-Internationalization-and-Localization.md)**  

**Concurrency**  
    46. **[Threading](./[46]-Threading.md)**  
    47. **[Multiprocessing](./[47]-Multiprocessing.md)**  
    48. **[Asyncio & concurrent.futures](./[48]-Asyncio.md)**  

**Networking**  
    49. **[Sockets & Networking Basics (TCP/UDP, socket module)](./[49]-Sockets-and-Networking.md)**  
    50. **[HTTP Clients (requests, httpx)](./[50]-HTTP-Clients.md)**  
    51. **[WebSockets in Python (websockets, FastAPI WebSockets)](./[51]-WebSockets.md)**  
    52. **[gRPC with Python](./[52]-gRPC.md)**  

**Security**  
    53. **[Hashing & Encryption (hashlib, secrets, cryptography library)](./[53]-Hashing-and-Encryption.md)**  
    54. **[Authentication & Authorization (JWT, OAuth2, passlib)](./[54]-Authentication-and-Authorization.md)**  
    55. **[Secure Coding Practices](./[55]-Secure-Coding-Practices.md)**  

**Data Science & Numerical Computing**  
    56. **[NumPy: Arrays, Broadcasting & Vectorization](./[56]-NumPy.md)**  
    57. **[Pandas: DataFrames, Series & Data Wrangling](./[57]-Pandas.md)**  
    58. **[Data Visualization: Matplotlib, Seaborn & Plotly](./[58]-Data-Visualization.md)**  
    59. **[SciPy: Scientific Computing](./[59]-SciPy.md)**  
    60. **[Statsmodels](./[60]-Statsmodels.md)**  

**Machine Learning & AI**  
    61. **[scikit-learn](./[61]-scikit-learn.md)**  
    62. **[TensorFlow & Keras](./[62]-TensorFlow-and-Keras.md)**  
    63. **[PyTorch](./[63]-PyTorch.md)**  
    64. **[XGBoost & LightGBM](./[64]-XGBoost-and-LightGBM.md)**  
    65. **[Hugging Face Transformers](./[65]-Hugging-Face-Transformers.md)**  
    66. **[Natural Language Processing: NLTK & spaCy](./[66]-NLTK-and-spaCy.md)**  
    67. **[Computer Vision: OpenCV](./[67]-OpenCV.md)**  

**Web Development**  
    68. **[Flask](./[68]-Flask.md)**  
    69. **[Django](./[69]-Django.md)**  
    70. **[FastAPI](./[70]-FastAPI.md)**  
    71. **[API Documentation (OpenAPI/Swagger)](./[71]-API-Documentation-OpenAPI.md)**  
    72. **[GraphQL with Python (Strawberry, Graphene)](./[72]-GraphQL-with-Python.md)**  
    73. **[Working with APIs & JSON (requests library)](./[73]-APIs-and-JSON.md)**  
    74. **[Web Scraping: BeautifulSoup & Scrapy](./[74]-Web-Scraping.md)**  
    75. **[Templating with Jinja2](./[75]-Jinja2-Templating.md)**  
    76. **[Building Data Apps with Streamlit](./[76]-Streamlit.md)**  

**Databases**  
    77. **[Working with Databases: SQLite3](./[77]-SQLite3.md)**  
    78. **[SQLAlchemy (ORM)](./[78]-SQLAlchemy.md)**  
    79. **[Connecting to PostgreSQL & MySQL (psycopg2, PyMySQL)](./[79]-PostgreSQL-and-MySQL.md)**  
    80. **[MongoDB with PyMongo](./[80]-MongoDB-PyMongo.md)**  
    81. **[Caching with Redis](./[81]-Caching-with-Redis.md)**  

**Messaging & Event-Driven Systems**  
    82. **[Task Queues with Celery](./[82]-Celery.md)**  
    83. **[Apache Kafka with Python (confluent-kafka, kafka-python)](./[83]-Apache-Kafka.md)**  
    84. **[RabbitMQ with Python (pika)](./[84]-RabbitMQ.md)**  

**Automation & Scripting**  
    85. **[Browser Automation with Selenium & Playwright](./[85]-Selenium-and-Playwright.md)**  
    86. **[Desktop Automation with PyAutoGUI](./[86]-PyAutoGUI.md)**  
    87. **[Excel Automation: openpyxl & xlrd](./[87]-Excel-Automation.md)**  
    88. **[Task Scheduling: schedule & APScheduler](./[88]-Task-Scheduling.md)**  

**GUI Development**  
    89. **[Tkinter](./[89]-Tkinter.md)**  
    90. **[PyQt & PySide](./[90]-PyQt-and-PySide.md)**  
    91. **[Kivy](./[91]-Kivy.md)**  

**Design Patterns & Architecture**  
    92. **[SOLID Principles in Python](./[92]-SOLID-Principles.md)**  
    93. **[Creational Patterns (Singleton, Factory, Builder)](./[93]-Creational-Design-Patterns.md)**  
    94. **[Structural Patterns (Adapter, Decorator, Proxy)](./[94]-Structural-Design-Patterns.md)**  
    95. **[Behavioral Patterns (Observer, Strategy, Command)](./[95]-Behavioral-Design-Patterns.md)**  
    96. **[Architectural Patterns (MVC, Layered, Hexagonal/Clean Architecture)](./[96]-Architectural-Patterns.md)**  

**Testing & Code Quality**  
    97. **[Testing: unittest, pytest & doctest](./[97]-Testing.md)**  
    98. **[Mocking (unittest.mock)](./[98]-Mocking.md)**  
    99. **[Property-Based Testing with Hypothesis](./[99]-Hypothesis.md)**  
    100. **[Testing Across Environments with tox](./[100]-Tox.md)**  
    101. **[Linters & Formatters: flake8, pylint, black, isort](./[101]-Linters-and-Formatters.md)**  
    102. **[Code Coverage (coverage.py)](./[102]-Code-Coverage.md)**  

**Packaging & Deployment**  
    103. **[Packaging & Distributing Your Code (pyproject.toml, setuptools, wheel, twine)](./[103]-Packaging-and-Distribution.md)**  
    104. **[Docker Basics for Python Apps](./[104]-Docker-Basics.md)**  
    105. **[Kubernetes Fundamentals for Python Services](./[105]-Kubernetes-Fundamentals.md)**  
    106. **[CI/CD Basics (GitHub Actions)](./[106]-CICD-Basics.md)**  
    107. **[Observability: Metrics, Tracing & Health Checks](./[107]-Observability.md)**  

**Performance & Optimization**  
    108. **[Profiling: cProfile & timeit](./[108]-Profiling.md)**  
    109. **[Cython](./[109]-Cython.md)**  
    110. **[Numba (JIT Compilation)](./[110]-Numba.md)**  

**Best Practices**  
    111. **[Pythonic Style & PEP 8](./[111]-Best-Practices-and-Style.md)**