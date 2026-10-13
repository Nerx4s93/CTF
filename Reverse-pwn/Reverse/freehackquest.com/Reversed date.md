Описание:
— Слышал когда-нибудь о Майкрософте?
— ...
— Вот и я не слышал. А недавно они прислали голубей с почтой. Голубей я, конечно, съел, а с вложением разобраться не смог. Поможешь? В долгу не останусь.
[MicrosoftProgram.exe](https://freehackquest.com/public/quests/5DDA7B71-5904-85F7-FBD1-AE5887EBAFAC_D51FE158-68A1-21EE-D059-0262D0BDF94D)

``` C#
using System;
using System.Linq;
using System.Text;

namespace DateReverse
{
	// Token: 0x02000002 RID: 2
	internal class Program
	{
		// Token: 0x06000001 RID: 1 RVA: 0x00002050 File Offset: 0x00000250
		private static void Main(string[] args)
		{
			DateTime now = DateTime.Now;
			Console.WriteLine((now.DayOfYear == 94 && now.Year == 2016 && now.DayOfWeek == DayOfWeek.Friday) ? Program.GetFlag() : "Sorry, try it another day");
		}

		// Token: 0x06000002 RID: 2 RVA: 0x0000209C File Offset: 0x0000029C
		private static string GetFlag()
		{
			byte[] array = new byte[]
			{
				156, 62, 233, 105, 196, 246, 143, 208, 55, 200,
				146, 41, 147, 214, 246, 247, 240, 217, 184, 32,
				7, 249, 108, 188, 106, 239, 170, 245, 53, 24,
				26, 53, 10, 145, 159, 61, 160
			};
			byte[] key = (from x in Enumerable.Range(0, 37)
				select (byte)Program.rand.Next(255)).ToArray<byte>();
			byte[] array2 = array.Select((byte t, int i) => t ^ key[i]).ToArray<byte>();
			return Encoding.UTF8.GetString(array2);
		}

		// Token: 0x04000001 RID: 1
		private static Random rand = new Random(50616);
	}
}
```

Дату менять я не буду поэтому патчу программу:
``` C#
private static void Main(string[] args)
{
	Console.WriteLine(Program.GetFlag());
}
```

![](z.%20Images/{121D9BC1-E5F1-420E-A6BA-E38706BD25E4}.png)

Ответ: `QCTF_22b21624c7119f3775910a7b1f89d5fa `