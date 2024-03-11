# Shamir-protocol


using System;
using System.Security.Cryptography;

namespace ShamirProtocol
{
    class Program
    {
        static void Main(string[] args)
        {
            // Генерация простых чисел p и q
            RandomNumberGenerator rng = RandomNumberGenerator.Create();
            byte[] pBytes = new byte[2];
            byte[] qBytes = new byte[2];
            rng.GetBytes(pBytes);
            rng.GetBytes(qBytes);
            int p = (int)(BitConverter.ToUInt16(pBytes, 0));
            int q = (int)(BitConverter.ToUInt16(qBytes, 0));
            int n = p * q;

            string secret = "Земля плоская";
            byte[] secretBytes = System.Text.Encoding.ASCII.GetBytes(secret);
            int S = BitConverter.ToInt32(secretBytes, 0);

            // v - открытый ключ
            int v = ModPow(S, 2, n);

            int count = 0;
            int no_of_iterations = 5;
            Console.WriteLine("Сообщение\t\t= " + S);
            Console.WriteLine("Простое число\t\t= " + n);

            for (int i = 0; i < no_of_iterations; i++)
            {
                // Алиса выбирает случайное число r в интервале (1, n-1) и отправляет x=(r^2)%n Бобу
                Random random = new Random();
                int r = random.Next(1, n - 1);
                int x = ModPow(r, 2, n);

                // Боб случайным образом выбирает бит e (0 или 1) и отправляет его Алисе
                int e = random.Next(0, 2);

                // Алиса вычисляет y=(r*(S^e))%n и отправляет его Бобу
                int y = (int)(((long)r * ModPow(S, e, n)) % n);

                // Боб проверяет равенство y^2 == (x*(v^e))%n. Если это верно, он переходит к следующему раунду протокола, в противном случае доказательство не принимается.
                Console.WriteLine("=====================================================");
                Console.WriteLine("Итерация " + (i + 1));
                Console.WriteLine("r\t\t= " + r);
                Console.WriteLine("x\t\t= " + x);
                Console.WriteLine("e\t\t= " + e);
                Console.WriteLine("y\t\t= " + y);
                Console.WriteLine("y^2\t\t= " + ModPow(y, 2, n));
                Console.WriteLine("(x*(v^e))\t= " + (((long)x * ModPow(v, e, n)) % n));

                Console.Write("\nИтерация " + (i + 1) + ":\t");
                if (ModPow(y, 2, n) == (((long)x * ModPow(v, e, n)) % n))
                {
                    Console.WriteLine("Пройдено");
                    count++;
                }
                else
                {
                    Console.WriteLine("Неудача");
                }
                Console.WriteLine("=====================================================");
            }

            if (count == no_of_iterations)
            {
                Console.WriteLine("У Алисы есть секрет.");
            }
            else
            {
                Console.WriteLine("У Алисы нет секрета.");
            }
        }

        // Пользовательская функция ModPow для работы с отрицательными показателями степени
        static int ModPow(int b, int e, int m)
        {
            if (e < 0)
            {
                e += m - 1;
            }
            return (int)Math.Pow(b, e) % m;
        }
    }
}



