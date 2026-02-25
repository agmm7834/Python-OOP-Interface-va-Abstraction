# Python OOP: Interface va Abstraction (Mavhum Sinflar)

---

## 1. Abstraction (Mavhumlashtirish) nima?

**Abstraction** — bu murakkab tafsilotlarni yashirib, faqat kerakli qismini ko'rsatish demak.

Oddiy misol: Avtomobil haydaysiz. Dvigatel qanday ishlashini bilmasangiz ham, rulni aylantirasiz va gaz bosasiz. Ichki murakkablik sизdan yashirilgan — bu abstraction!

---

## 2. Python'da Abstraction: `ABC` moduli

Python'da abstraction uchun `abc` (Abstract Base Class) moduli ishlatiladi.

```python
from abc import ABC, abstractmethod

class Hayvon(ABC):  # Mavhum sinf
    
    @abstractmethod
    def ovoz_chiqar(self):
        pass  # Bu yerda kod yo'q, faqat "qoida" belgilanadi
    
    @abstractmethod
    def harakat_qil(self):
        pass
    
    def nafas_ol(self):  # Bu oddiy metod - mavhum emas
        print("Nafas olmoqda...")
```

Agar `Hayvon` sinfidan to'g'ridan-to'g'ri obyekt yaratmoqchi bo'lsak, xato chiqadi:

```python
h = Hayvon()  # ❌ TypeError: Can't instantiate abstract class
```

To'g'ri ishlatish:

```python
class It(Hayvon):
    def ovoz_chiqar(self):
        print("Vov vov!")
    
    def harakat_qil(self):
        print("Yugurmoqda...")

class Mushuk(Hayvon):
    def ovoz_chiqar(self):
        print("Miyov!")
    
    def harakat_qil(self):
        print("Sakramoqda...")

it = It()
mushuk = Mushuk()

it.ovoz_chiqar()      # Vov vov!
mushuk.ovoz_chiqar()  # Miyov!
it.nafas_ol()         # Nafas olmoqda...
```

---

## 3. Interface nima?

**Interface** — bu sinfning "shartnomasi". U sinfning qanday metodlarga ega bo'lishi kerakligini belgilaydi, lekin ularning ichidagi kodini yozmaydi.

> ⚠️ Python'da `interface` kalit so'zi yo'q (Java'dan farqli). Buning o'rniga `ABC` va `@abstractmethod` ishlatiladi.

**Farq:**
- **Abstraction** — ba'zi metodlar mavhum, ba'zilari emas
- **Interface** — barcha metodlar mavhum (faqat `pass`)

```python
from abc import ABC, abstractmethod

# Bu aslida "Interface" vazifasini o'taydi
class TolovInterface(ABC):
    
    @abstractmethod
    def tolov_qil(self, summa: float) -> bool:
        pass
    
    @abstractmethod
    def qaytarish(self, summa: float) -> bool:
        pass
    
    @abstractmethod
    def balans_korish(self) -> float:
        pass
```

---

## 4. Real Loyiha Misoli: To'lov Tizimi

```python
from abc import ABC, abstractmethod

# ==================== INTERFACE ====================
class TolovInterface(ABC):
    
    @abstractmethod
    def tolov_qil(self, summa: float) -> bool:
        pass
    
    @abstractmethod
    def qaytarish(self, summa: float) -> bool:
        pass
    
    @abstractmethod
    def balans_korish(self) -> float:
        pass


# ==================== IMPLEMENTATSIYALAR ====================
class PaymeTolov(TolovInterface):
    def __init__(self, balans: float):
        self._balans = balans  # _ = himoyalangan atribut
    
    def tolov_qil(self, summa: float) -> bool:
        if self._balans >= summa:
            self._balans -= summa
            print(f"Payme: {summa:,.0f} so'm to'landi ✅")
            return True
        print("Payme: Mablag' yetarli emas ❌")
        return False
    
    def qaytarish(self, summa: float) -> bool:
        self._balans += summa
        print(f"Payme: {summa:,.0f} so'm qaytarildi 🔄")
        return True
    
    def balans_korish(self) -> float:
        return self._balans


class ClickTolov(TolovInterface):
    def __init__(self, balans: float):
        self._balans = balans
    
    def tolov_qil(self, summa: float) -> bool:
        if self._balans >= summa:
            self._balans -= summa
            print(f"Click: {summa:,.0f} so'm to'landi ✅")
            return True
        print("Click: Mablag' yetarli emas ❌")
        return False
    
    def qaytarish(self, summa: float) -> bool:
        self._balans += summa
        print(f"Click: {summa:,.0f} so'm qaytarildi 🔄")
        return True
    
    def balans_korish(self) -> float:
        return self._balans


# ==================== ISHLATISH ====================
class OnlineDukon:
    def __init__(self, tolov_usuli: TolovInterface):
        self.tolov = tolov_usuli  # Har qanday to'lov usulini qabul qiladi!
    
    def xarid_qil(self, mahsulot: str, narx: float):
        print(f"\n🛒 '{mahsulot}' sotib olinyapti...")
        muvaffaqiyat = self.tolov.tolov_qil(narx)
        if muvaffaqiyat:
            print(f"'{mahsulot}' muvaffaqiyatli sotib olindi!")
        print(f"Joriy balans: {self.tolov.balans_korish():,.0f} so'm")


# Payme bilan ishlatish
payme = PaymeTolov(500_000)
dukon1 = OnlineDukon(payme)
dukon1.xarid_qil("Telefon qoplag'ich", 45_000)
dukon1.xarid_qil("Quloqchin", 600_000)

print("\n" + "="*40 + "\n")

# Click bilan ishlatish - KOD O'ZGARMADI!
click = ClickTolov(1_000_000)
dukon2 = OnlineDukon(click)
dukon2.xarid_qil("Kitob", 120_000)
```

**Natija:**
```
🛒 'Telefon qoplag'ich' sotib olinyapti...
Payme: 45,000 so'm to'landi ✅
'Telefon qoplag'ich' muvaffaqiyatli sotib olindi!
Joriy balans: 455,000 so'm

🛒 'Quloqchin' sotib olinyapti...
Payme: Mablag' yetarli emas ❌
Joriy balans: 455,000 so'm
```

---

## 5. Ko'p Interfeys (Multiple Interface)

Python bir sinfga bir nechta interfeys qo'llashga ruxsat beradi:

```python
from abc import ABC, abstractmethod

class Chop_etish(ABC):
    @abstractmethod
    def chop_et(self, matn: str):
        pass

class Skanerlash(ABC):
    @abstractmethod
    def skaner_qil(self) -> str:
        pass

class Faks_yuborish(ABC):
    @abstractmethod
    def faks_yuborish(self, raqam: str):
        pass


# Ko'p interfeys implement qilish
class KoPrinteri(Chop_etish, Skanerlash, Faks_yuborish):
    
    def chop_et(self, matn: str):
        print(f"Chop etilmoqda: {matn}")
    
    def skaner_qil(self) -> str:
        print("Skanerlash jarayoni...")
        return "Skaner natijasi"
    
    def faks_yuborish(self, raqam: str):
        print(f"{raqam} raqamiga faks yuborilmoqda...")


class OddiyPrinter(Chop_etish):  # Faqat chop etadi
    def chop_et(self, matn: str):
        print(f"Oddiy chop: {matn}")


# Ishlatish
kop = KoPrinteri()
kop.chop_et("Salom Dunyo!")
kop.skaner_qil()
kop.faks_yuborish("+998901234567")

print()
oddiy = OddiyPrinter()
oddiy.chop_et("Oddiy matn")
```

---

## 6. Property bilan Abstraction

`@property` dekoratori orqali atributlarni yashirish (encapsulation + abstraction):

```python
from abc import ABC, abstractmethod

class Shakl(ABC):
    
    @property
    @abstractmethod
    def yuza(self) -> float:
        pass
    
    @property
    @abstractmethod
    def perimetr(self) -> float:
        pass
    
    def tavsif(self):
        print(f"Yuza: {self.yuza:.2f}, Perimetr: {self.perimetr:.2f}")


class Doira(Shakl):
    def __init__(self, radius: float):
        self._radius = radius
    
    @property
    def yuza(self) -> float:
        return 3.14159 * self._radius ** 2
    
    @property
    def perimetr(self) -> float:
        return 2 * 3.14159 * self._radius


class Toʻrtburchak(Shakl):
    def __init__(self, eni: float, boyi: float):
        self._eni = eni
        self._boyi = boyi
    
    @property
    def yuza(self) -> float:
        return self._eni * self._boyi
    
    @property
    def perimetr(self) -> float:
        return 2 * (self._eni + self._boyi)


# Ishlatish
shakllar = [Doira(5), Toʻrtburchak(4, 6), Doira(3)]

for shakl in shakllar:
    print(type(shakl).__name__ + ":")
    shakl.tavsif()
    print()
```

**Natija:**
```
Doira:
Yuza: 78.54, Perimetr: 31.42

Toʻrtburchak:
Yuza: 24.00, Perimetr: 20.00

Doira:
Yuza: 28.27, Perimetr: 18.85
```

---

## 7. `isinstance()` bilan tekshirish

```python
from abc import ABC, abstractmethod

class Transport(ABC):
    @abstractmethod
    def yur(self):
        pass

class Mashina(Transport):
    def yur(self):
        print("Mashina yuryapti")

class Velosiped(Transport):
    def yur(self):
        print("Velosiped yuryapti")


mashina = Mashina()
velo = Velosiped()

# Interfeys tekshiruvi
print(isinstance(mashina, Transport))   # True
print(isinstance(velo, Transport))      # True
print(isinstance(mashina, Velosiped))  # False

# Ro'yxatdan foydalanish
transportlar = [Mashina(), Velosiped(), Mashina()]
for t in transportlar:
    if isinstance(t, Transport):
        t.yur()
```

---

## 8. Abstraction vs Encapsulation — Farqi nima?

| | **Abstraction** | **Encapsulation** |
|---|---|---|
| **Maqsad** | Murakkablikni yashirish | Ma'lumotni himoya qilish |
| **Vosita** | `ABC`, `@abstractmethod` | `_`, `__`, `@property` |
| **Savol** | *Nima qiladi?* | *Qanday saqlanadi?* |
| **Misol** | `def tolov_qil()` metodi | `self.__balans = 1000` |

```python
class BankHisobi:
    def __init__(self, balans):
        self.__balans = balans  # Encapsulation: yashirilgan
    
    # Abstraction: foydalanuvchi faqat shu metodni ko'radi
    def pul_qo_yish(self, summa):
        if summa > 0:
            self.__balans += summa
    
    def balans(self):
        return self.__balans
```

---

## 9. Xulosa

```
Abstraction = "Nima qilishini ayt, qanday qilishini yashir"
Interface   = "Bu qoidalar bajarilishi shart" degan shartnoma
```

**Qachon ishlatish kerak?**

- **Abstraction** — turli sinflar uchun umumiy qolip kerak bo'lganda
- **Interface** — bir xil metodlarga ega bo'lishi shart bo'lgan sinflar yaratganda
- **Ikkalasi birga** — katta loyihalarda, dastur arxitekturasini tartibga solishda

**Asosiy qoidalar:**
1. `from abc import ABC, abstractmethod` import qiling
2. Mavhum sinfni `ABC`dan meros oling
3. Mavhum metodlarga `@abstractmethod` qo'ying
4. Bolа sinflar barcha mavhum metodlarni yozishi shart
5. Mavhum sinfdan to'g'ridan-to'g'ri obyekt yaratib bo'lmaydi

---
