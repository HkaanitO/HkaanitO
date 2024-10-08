- 👋 Hi, I’m @HkaanitO
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
HkaanitO/HkaanitO is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

/*COOOK ONEMLI HATIRLATMA BU KODLARIN KUTUPHANELERI
KESINLIKLE BUNLAR DEGILDIR VE KODUN HANGI DILDE YAZILDIGI
KESTIRILEMEMEKTEDIR O YUZDEN NASIL ANLASILIYORSA
O SEKILDE ACIKLAMA YAPILMAYA CALISILMISTIR.*/



#include <iostream>

using namespace std;
#pragma once

struct PID {
	static const int encoderPinDigital = 30;//Digital encoder pin numarasi tanimlandi
	static const int encoderPinInterrupt = 31;//Encoder interrupt pin tanimlandi.

	static const int closePin = 28;
	static const int openPin = 29;
	//Buraya kadar olan kisim, degiskenleri islemciden biran önce kaldirmak icin static ile kullandi
	// Ayni sekilde const ifadesi degistirilememesi icin birakildi.
	//Öyle korkulacak seyler yok

	//Tahminimce kameranin ilk motaji dogru sekilde yapilacak ve sistemin calismasiyla beraber 
	// Kameranin ilk montaj konumu dogru pozisyon olarak tanimlanacak
	//bu yüzden tanimlanan ilk hata poziysonu(errorPosition) 0 kabul edildi.
	float errorPosition = 0;

	//Yazildiktan sonra kullanilacak olan fonksiyonlarin adlarindan bahsedildi.#

	void setup();
	void update();
	void PIDgenerator();

};

int main() {

	float errorIntegral = 0.0;
	float previouserrorPosition = 0;
	float previousTime = 0;
	float numberofTurn = 4.1;
	float target_motor_position = 0;
	float encoderRead = 0;
	float controlSignal = 0;
	float currentTime;
	const float Ki = 0.69;
	const float Kp = 1.5;
	int PWMValue = 0;
	//Yukaridaki kisimda diger degiskenler tanimlandi

	static volatile float motorPosition = 0;//Volatile bir nesnesin donanim tarafindan degistirilebilecegi tanimlandi
	static void driveHSMotor(); //Kameranin sabit durmasi icin kullanilacak olan motoru Sürmek icin olan func.
	static void PIDgenerator();//Her hata (kameranin istenmeyen yöne dönmesi) ciktiginda PID kontrolünün cikip durumu degerlendirmesi ve uygun olani yapmasi.
	static void PositionGenerator();//Hata durumu saptanip PIDgenerator devreye sokulduktan sonra yeni Position ayari veya hatali konumun saptanmasi.

	void PID::setup() { //Classta belirtilen fonksiyon tanimi
		pinMode(encoderPinDigital, INPUT); //Burada 
		pinMode(encoderPinInterrupt, INPUT);//Ve burada Input pinleri belirlendi
		attachInterrupt(digitalPinToInterrupt(PID::encoderPinInterrupt), PositionGenerator, RISING)//Interrupt kullanimi ögrenilmeli(https://projedenemeleri.blogspot.com/2018/05/arduinoda-ds-kesme-external-interrupt.html)
	}

	void PID::update() {//Classda bahsedilen kütüphanenin islenmesi
		PIDgenerator();
		driveHSMotor();
	}

	static void driveHSMotor() { //main partta bahsedilen kütüp yazilmasi (::) baglaci yok
		PWMValue = (int)fabs(controlSignal)
			if (PWMValue >= 255)
				PWMValue = 255;
			else
				PWMValue = PWMValue;
	
	if (controlSignal <= 0) {
		analogWrite(PID::openPin, PWMValue);//Arduinodan bilinen klasik komutlar
		digitalWrite(PID::closePin, LOW);//Arduinodan bilinen klasik komutlar
	}
	else if (controlSignal >= 0) {
		analogWrite(PID::closePin, PMWValue);//Arduinodan bilinen klasik komutlar
		digitalWrite(PID::openPin, LOW);//Arduinodan bilinen klasik komutlar
	}
	else if (controlSignal == 0) {
		digitalWrite(PID::openPin, LOW);//Arduinodan bilinen klasik komutlar
		digitalWrite(PID::closePin, LOW);//Arduinodan bilinen klasik komutlar
	}
}
	void PID::PIDgenerator() { //Class tarafinda bahsedilen fonksiyon tanimlanmasi

		currentTime = micros();//Zaman mikrosaniye cinsinden tutuldu
		auto deltaTime = (currentTime - previousTime) / 1e6;
		previousTime = currentTime; //

		errorPosition = target_motor_position - motorPosition;//error Positioni bulundu

		errorIntegral = errorIntegral + (PID::errorPosition + previouserrorPosition) * 0.5 * deltaTime;//Error olayi integral ile ifade edildi.

		if (errorIntegral >= 50)
			errorIntegral = 50;
		else if (errorIntegral <= -50)
			errorIntegral = -50;
		else {
			errorIntegral = errorIntegral;
		}//Bu if structure'inda errorIntegral olayin göre belirli ayarlamalar yapildi.

		controlSignal = (Kp * PID::errorPosition) + (Ki * errorIntegral);//Kontrol sinyali islemleri
		previouserrorPosition = PID::errorPosition;//önceki hatali konum tutuluyor
	}
	void PositionGenerator() { //Bir baska fonksiyon tanimlandi ve kullanildi
		encoderRead = digitalRead(PID::encoderPinDigital);

		if (encoderRead == 1) //CW direction
		{
			motorPosition++;
		}
		else //else, it is zero CCW direction
		{
			motorPosition--;
		}
	}


	











