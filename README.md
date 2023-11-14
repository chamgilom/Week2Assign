using System;

namespace Week2Assign
{
    public class Character
    {
        public string Name { get; } //한번 정해지면 바꿀 수 없게
        public string Job { get; }
        public int Level { get; }
        public int Atk { get; }
        public int Def { get; }
        public int Hp { get; }
        public int Gold { get; }

        public Character(string name, string job, int level, int atk, int def, int hp, int gold)// 생성자: 클래스의 이름과 같음, 생성할 때 이런 기능도 캍이 했으면 좋을 때 사용
        {
            Name = name;
            Job = job;
            Level = level;
            Atk = atk;
            Def = def;
            Hp = hp;
            Gold = gold; // 클래스는 설계도, 객체는 설계도를 바탕으로 소프트웨어 세계에 구현된 구체적인 실체, 인스턴스는 설계도를 바탕으로 소프트웨어 세계에 구현된 구체적인 실체
        }
    }

    public class Item
    {
        public string Name { get; }
        public string Description { get; }

        // 개선포인트 : Enum 활용
        public int Type { get; }

        public int Atk { get; }
        public int Def { get; }
        public int Hp { get; }


        public bool IsEquiped { get; set; } //장착이 되었는지를 true와 false로 구분

        public static int ItemCnt = 0; //클래스 ItemCnt에 공유되는 static 변수를 붙여서 Item이라는 클래스에 공유되게 만든다.

        public Item(string name, string description, int type, int atk, int def, int hp, bool isEquiped = false)//isEquiped = false 처음 시작했을때 아이템 안끼고 있기
        {
            Name = name;
            Description = description;
            Type = type;
            Atk = atk;
            Def = def;
            Hp = hp;
            IsEquiped = isEquiped;
        }

        public void PrintItemStatDescription(bool withNumber = false, int idx = 0)
        {
            Console.Write("- ");
            // 장착관리 전용
            if (withNumber) //withNumber가 참일때
            {
                Console.ForegroundColor = ConsoleColor.DarkMagenta;
                Console.Write("{0} ", idx);
                Console.ResetColor();
            }
            if (IsEquiped)
            {
                Console.Write("[");
                Console.ForegroundColor = ConsoleColor.Cyan;
                Console.Write("E");
                Console.ResetColor();
                Console.Write("]");
                Console.Write(PadRightForMixedText(Name, 9));
            }
            else Console.Write(PadRightForMixedText(Name, 12));

            Console.Write(" | ");

            if (Atk != 0) Console.Write($"Atk {(Atk >= 0 ? "+" : "")}{Atk} ");// 삼항연산자, $를 쓰면 중괄호에 있는 것을 변수로 쓸 수 있다.
            if (Def != 0) Console.Write($"Def {(Def >= 0 ? "+" : "")}{Def} ");// 조건 ? 조건이 참이라면 실행될 코드 : 조건이 거짓이면 실행될 코드
            if (Hp != 0) Console.Write($"Hp {(Hp >= 0 ? "+" : "")}{Hp}");

            Console.Write(" | ");

            Console.WriteLine(Description);
        }

        public static int GetPrintableLength(string str)
        {
            int length = 0;
            foreach (char c in str)
            {
                if (char.GetUnicodeCategory(c) == System.Globalization.UnicodeCategory.OtherLetter)
                {
                    length += 2; // 한글과 같은 넓은 문자에 대해 길이를 2로 취급
                }
                else
                {
                    length += 1; // 나머지 문자에 대해 길이를 1로 취급
                }
            }

            return length;
        }

        public static string PadRightForMixedText(string str, int totalLength)
        {
            int currentLength = GetPrintableLength(str);
            int padding = totalLength - currentLength;
            return str.PadRight(str.Length + padding);
        }

    }

    internal class Program
    {
        static Character _player; //플레이어 생성
        static Item[] _items; //아이템 여러개 끼고 있기

        static void Main(string[] args)
        {
            /// 구성 
            /// 0. 초기화함
            /// 1. 스타팅 로고를 보여줌 (게임 처음 킬때만 보여줌)
            /// 2. 선택 화면을 보여줌 (기본 구현사항 - 상태 / 인벤토리)
            /// 3. 상태화면을 구현함 (필요 구현 요소 : 캐릭터, 아이템)
            /// 4. 인벤토리 화면을 구현함
            GameDataSetting();
            PrintStartLogo();//아스키 아트로 설정
            StartMenu();
        }

        static void GameDataSetting()
        {
            _player = new Character("chad", "전사", 1, 10, 5, 100, 1500);
            _items = new Item[10];
            AddItem(new Item("무쇠갑옷", "무쇠로 만들어져 튼튼한 갑옷입니다.", 0, 0, 5, 0));// (string name, string description, int type, int atk, int def, int hp, bool isEquiped)에 따라서 숫자를 넣음
            AddItem(new Item("낡은 검", "쉽게 볼 수 있는 낡은 검입니다.", 1, 2, 0, 0));//bool은 착용하고 시작할 것이 아니기 때문에 적지 않는다.
            AddItem(new Item("골든 헬름", "희귀한 광석으로 만들어진 투구입니다.", 1, 0, 9, 0));//name=골든헬름,description=희귀한 광석으로 만들어진 투구입니다.,type=1, atk=0, def=9, hp=0
        }

        static void StartMenu()
        {
            /// 구성
            /// 0. 화면 정리
            /// 1. 선택 멘트를 줌
            /// 2. 선택 결과값을 검증함
            /// 3. 선택 결과에 따라 메뉴로 보내줌
            Console.Clear();

            Console.WriteLine("■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■");
            Console.WriteLine("스파르타 마을에 오신 여러분 환영합니다.");
            Console.WriteLine("이곳에서 던전으로 들어가기 전 활동을 할 수 있습니다.");
            Console.WriteLine("■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■■");
            Console.WriteLine("");
            Console.WriteLine("1. 상태 보기");
            Console.WriteLine("2. 인벤토리");
            Console.WriteLine("");
            // 1안 : 착한 유저들만 있을 경우
            // int keyInput = int.Parse(Console.ReadLine());

            // 2안 : 나쁜 유저들도 있는 경우
            // int keyInput;
            // bool result;
            // do
            // {
            //     Console.WriteLine("원하시는 행동을 입력해주세요.");
            //     result = int.TryParse(Console.ReadLine(), out keyInput);
            // } while (result == false || CheckIfValid(keyInput, min : 1, max : 2) == false);

            switch (CheckValidInput(1, 2)) // 결과값에 따라서 코드를 실행시킬 것이다
            {
                case 1://값이 1일떄
                    StatusMenu();
                    break;
                case 2://값이 2일때
                    InventoryMenu();
                    break;
            }
        }

        static int CheckValidInput(int min, int max)
        {
            /// 설명
            /// 아래 두 가지 상황은 비정상 -> 재입력 수행
            /// (1) 숫자가 아닌 입력을 받은 경우
            /// (2) 숫자가 최소값 ~ 최대값의 범위를 넘는 경우
            int keyInput;//tryparse에 필요
            bool result;//while문에 필요
            do//일단 한번 실행
            {
                Console.WriteLine("원하시는 행동을 입력해주세요.");
                result = int.TryParse(Console.ReadLine(), out keyInput);//TryParse: 숫자일 수도 있고 아닐수도 있음 out keyInput: 가져오기
            } while (result == false || CheckIfValid(keyInput, min, max) == false);//result == false 틀리면 다시 뽑기, keyInput이 min과 max 사이에 없을때 반복하기

            return keyInput;
        }

        static bool CheckIfValid(int checkable, int min, int max)
        {
            if (min <= checkable && checkable <= max) return true;
            return false;
        }

        static void AddItem(Item item)
        {
            if (Item.ItemCnt == 10) return; //아이템의 숫자가 10을 넘으면 아무일도 안일어남
            _items[Item.ItemCnt] = item; //아이템이 0개면 0번 인덱스, 1개면 1번,...
            Item.ItemCnt++; //아이템이 1개씩 증가
        }

        static void StatusMenu()
        {
            Console.Clear(); //화면이 지저분하니까 지우는 용도

            ShowHighlightedText("■ 상태 보기 ■");
            Console.WriteLine("캐릭터의 정보가 표기됩니다.");

            PrintTextWithHighlights("Lv. ", _player.Level.ToString("00"));// ToString에 00을 걸면 앞의 0이 생략되지 않는다.
            Console.WriteLine("");
            Console.WriteLine("{0} ( {1} )", _player.Name, _player.Job);// 0은 플레이어의 이름 chard, 1은 플레이어의 직업 전사로 넣을 수 있다.

            int bonusAtk = getSumBonusAtk();
            PrintTextWithHighlights("공격력 : ", (_player.Atk + bonusAtk).ToString(), bonusAtk > 0 ? string.Format(" (+{0})", bonusAtk) : "");

            int bonusDef = getSumBonusDef();
            PrintTextWithHighlights("방어력 : ", (_player.Def + bonusDef).ToString(), bonusDef > 0 ? string.Format(" (+{0})", bonusDef) : "");

            int bonusHp = getSumBonusHp();
            PrintTextWithHighlights("체 력 : ", (_player.Hp + bonusHp).ToString(), bonusHp > 0 ? string.Format(" (+{0})", bonusHp) : "");

            PrintTextWithHighlights("Gold : ", _player.Gold.ToString());
            Console.WriteLine("");
            Console.WriteLine("0. 뒤로가기");
            Console.WriteLine("");
            switch (CheckValidInput(0, 0))
            {
                case 0:
                    StartMenu();
                    break;
            }
        }

        private static int getSumBonusAtk()
        {
            int sum = 0;
            for (int i = 0; i < Item.ItemCnt; i++)
            {
                if (_items[i].IsEquiped) sum += _items[i].Atk;
            }
            return sum;
        }

        private static int getSumBonusDef()
        {
            int sum = 0;
            for (int i = 0; i < Item.ItemCnt; i++)
            {
                if (_items[i].IsEquiped) sum += _items[i].Def;
            }
            return sum;
        }

        private static int getSumBonusHp()
        {
            int sum = 0;
            for (int i = 0; i < Item.ItemCnt; i++)
            {
                if (_items[i].IsEquiped) sum += _items[i].Hp;
            }
            return sum;
        }

        /* 고급 문법 : 리플렉션 적용 문법
         private static int GetSumBonusString(string propertyName)
        {
            int sum = 0;
            for (int i = 0; i < Item.itemCnt; i++)
            {
                if (items[i].IsEquiped)
                {
                    // 리플렉션을 사용하여 현재 아이템의 propertyName 속성 값을 가져옵니다.
                    var propertyInfo = items[i].GetType().GetProperty(propertyName);
                    if (propertyInfo != null)
                    {
                        // 속성 값이 int 타입이라고 가정하고 값을 가져옵니다.
                        int value = (int)propertyInfo.GetValue(items[i], null);
                        sum += value;
                    }
                }
            }
            return sum;
        }
         */
        private static void PrintTextWithHighlights(string s1, string s2, string s3 = "") // 중간에 있는 글자의 색을 노란색으로 바꾸는 함수
        {
            Console.Write(s1);
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.Write(s2);
            Console.ResetColor();
            Console.WriteLine(s3);//WriteLine의 의미는 한줄을 끝내겠다는 의미
        }

        static void InventoryMenu()
        {
            Console.Clear();

            ShowHighlightedText("■ 인벤토리 ■");
            Console.WriteLine("보유 중인 아이템을 관리할 수 있습니다.");
            Console.WriteLine("");
            Console.WriteLine("[아이템 목록]");
            for (int i = 0; i < Item.ItemCnt; i++)
            {
                _items[i].PrintItemStatDescription();
            }
            Console.WriteLine("");
            Console.WriteLine("0. 나가기");
            Console.WriteLine("1. 장착관리");
            Console.WriteLine("");
            switch (CheckValidInput(0, 1))
            {
                case 0:
                    StartMenu();
                    break;
                case 1:
                    EquipMenu();
                    break;
            }
        }

        static void EquipMenu()
        {
            Console.Clear();

            ShowHighlightedText("■ 인벤토리 - 장착 관리 ■");
            Console.WriteLine("보유 중인 아이템을 관리할 수 있습니다.");
            Console.WriteLine("");
            Console.WriteLine("[아이템 목록]");
            for (int i = 0; i < Item.ItemCnt; i++)
            {
                _items[i].PrintItemStatDescription(true, i + 1); // 1, 2, 3에 매핑하기 위해 +1
            }
            Console.WriteLine("");
            Console.WriteLine("0. 나가기");

            int keyInput = CheckValidInput(0, Item.ItemCnt);

            switch (keyInput)
            {
                case 0:
                    InventoryMenu();
                    break;
                default:
                    ToggleEquipStatus(keyInput - 1); // 유저가 입력하는건 1, 2, 3 : 실제 배열에는 0, 1, 2...
                    EquipMenu();
                    break;
            }
        }

        static void ToggleEquipStatus(int idx)
        {
            _items[idx].IsEquiped = !_items[idx].IsEquiped;//toggle의 값을 반대로 바꾸는 기호는 느낌표이다.
        }

        static void ShowHighlightedText(string title)// 글자의 색을 바꾸기
        {
            Console.ForegroundColor = ConsoleColor.Magenta; // 글자의 색을 Magenta로 바꾸기
            Console.WriteLine(title);
            Console.ResetColor();//바꾼 색을 되돌리기
        }

        static void PrintStartLogo()
        {
            // ASCII ART GENERATED BY https://textkool.com/en/ascii-art-generator?hl=default&vl=default&font=Red%20Phoenix
            Console.WriteLine("=============================================================================");
            Console.WriteLine("        ___________________   _____  __________ ___________ _____    ");
            Console.WriteLine("       /   _____/\\______   \\ /  _  \\ \\______   \\\\__    ___//  _  \\   ");
            Console.WriteLine("       \\_____  \\  |     ___//  /_\\  \\ |       _/  |    |  /  /_\\  \\  ");
            Console.WriteLine("       /        \\ |    |   /    |    \\|    |   \\  |    | /    |    \\ ");
            Console.WriteLine("      /_______  / |____|   \\____|__  /|____|_  /  |____| \\____|__  / ");
            Console.WriteLine("              \\/                   \\/        \\/                  \\/  ");
            Console.WriteLine(" ________    ____ ___ _______     ________ ___________________    _______");
            Console.WriteLine(" \\______ \\  |    |   \\\\      \\   /  _____/ \\_   _____/\\_____  \\   \\      \\");
            Console.WriteLine("  |    |  \\ |    |   //   |   \\ /   \\  ___  |    __)_  /   |   \\  /   |   \\\r\n");
            Console.WriteLine("  |    |   \\|    |  //    |    \\\\    \\_\\  \\ |        \\/    |    \\/    |    \\\r\n");
            Console.WriteLine(" /_______  /|______/ \\____|__  / \\______  //_______  /\\_______  /\\____|__  /\r\n");
            Console.WriteLine("         \\/                  \\/         \\/         \\/         \\/         \\/");
            Console.WriteLine("=============================================================================");
            Console.WriteLine("                           PRESS ANYKEY TO START                             ");
            Console.WriteLine("=============================================================================");
            Console.ReadKey(); //아무 버튼이나 누르면 다음으로 넘어감
        }
    }


}
 

내가 짠 코드
using System;

namespace _2주차_과제
{
    internal class Program2

    {
        private static Character player;
        private static bool Equip1;
        private static bool Equip2;
        private static string input;


        static void Main(string[] args)
        {
            GameDataSetting();
            DisplayGameIntro();
        }

        static void GameDataSetting()
        {
            // 캐릭터 정보 세팅
            player = new Character("Chad", "전사", 1, 10, 5, 100, 1500);

            // 아이템 정보 세팅
        }

        static void DisplayGameIntro()
        {
            Console.Clear();

            Console.WriteLine("스파르타 마을에 오신 여러분 환영합니다.");
            Console.WriteLine("이곳에서 전전으로 들어가기 전 활동을 할 수 있습니다.");
            Console.WriteLine();
            Console.WriteLine("1. 상태보기");
            Console.WriteLine("2. 인벤토리");
            Console.WriteLine();
            Console.WriteLine("원하시는 행동을 입력해주세요.");

            int input = CheckValidInput(0, 0);
            switch (input)
            {
                case 1:
                    DisplayMyInfo();
                    break;

                case 2:
                    DisplayInventoryInfo();
                    break;

                default:
                    Console.WriteLine("잘못된 입력입니다!");
                    break;
            }
        }



        static void DisplayInventoryInfo()
        {
            Console.Clear();
            string[] AmountOfEquipment = new string[2]
           { "무쇠갑옷      | 방어력 +5 | 무쇠로 만들어져 튼튼한 갑옷입니다.", "낡은 검         | 공격력 +2 | 쉽게 볼 수 있는 낡은 검입니다." };


            Console.WriteLine("인벤토리");
            Console.WriteLine("보유 중인 아이템을 관리할 수 있습니다.");
            Console.WriteLine();
            Console.WriteLine("[아이템 목록]");
            for (int i = 0; i <= AmountOfEquipment.Length; i++)
            {

                if (Equip1 == true);
                {
                    Console.WriteLine("- [E]", AmountOfEquipment[1]);
                    return;
                }
                if (Equip1 == false);
                {
                    Console.WriteLine("-", AmountOfEquipment[i]);
                    return;

                if (Equip2 == true);
                    {
                        Console.WriteLine("- [E]", AmountOfEquipment[1]);
                        return;
                    }
                if (Equip2 == false);
                    {
                        Console.WriteLine("-", AmountOfEquipment[i]);
                        return;
                    }
                }
                Console.WriteLine();
                Console.WriteLine();
                Console.WriteLine("1. 장착 관리");
                Console.WriteLine("0. 나가기");
                Console.WriteLine();
                Console.WriteLine("원하시는 행동을 입력해주세요.");

                switch (input)
                {
                    case "0":
                        DisplayGameIntro();
                        break;

                    case "1":
                        DisplayInventory();
                        break;
                }
            }
            static void DisplayMyInfo()
            {

                Console.Clear();

                Console.WriteLine("상태보기");
                Console.WriteLine("캐릭터의 정보를 표시합니다.");

                Console.WriteLine();
                Console.WriteLine($"Lv.{player.Level}");
                Console.WriteLine($"{player.Name}({player.Job})");
                Console.WriteLine($"공격력 :{player.Atk}");
                Console.WriteLine($"방어력 : {player.Def}");
                Console.WriteLine($"체력 : {player.Hp}");
                Console.WriteLine($"Gold : {player.Gold} G");
                Console.WriteLine();
                Console.WriteLine("0. 나가기");
                Console.WriteLine();
                Console.WriteLine("원하시는 행동을 입력해주세요.");

                int input = CheckValidInput(0, 0);
                switch (input)
                {
                    case 0:
                        DisplayGameIntro();
                        break;
                }
            }

            static void DisplayInventory()
            {



                Console.WriteLine("0. 나가기");
                Console.WriteLine("1. 무쇠갑옷 장착하기");
                Console.WriteLine("2. 무쇠갑옷 장착해제하기");
                Console.WriteLine("3. 낡은 검 장착하기");
                Console.WriteLine("4. 낡은 검 장착해제하기");

                int input = CheckValidInput(0, 0);
                switch (input)
                {
                    case 0:
                        DisplayGameIntro();
                        break;
                    case 1:
                        Equip1 = true;
                        DisplayInventory();
                        break;
                    case 2:
                        Equip1 = false;
                        DisplayInventory();
                        break;
                    case 3:
                        Equip2 = true;
                        DisplayInventory();
                        break;
                    case 4:
                        Equip2 = false;
                        DisplayInventory();
                        break;


                }
            }

            static int CheckValidInput(int min, int max)
            {
                while (true)
                {
                    string input = Console.ReadLine();

                    bool parseSuccess = int.TryParse(input, out var ret);
                    if (parseSuccess)
                    {
                        if (ret >= min && ret <= max)
                            return ret;
                    }

                    Console.WriteLine("잘못된 입력입니다.");
                }
            }
        }


        public class Character
        {
            public string Name { get; }
            public string Job { get; }
            public int Level { get; }
            public int Atk { get; }
            public int Def { get; }
            public int Hp { get; }
            public int Gold { get; }

            public Character(string name, string job, int level, int atk, int def, int hp, int gold)
            {
                Name = name;
                Job = job;
                Level = level;
                Atk = atk;
                Def = def;
                Hp = hp;
                Gold = gold;
            }
        }
    }
}
        }         }
