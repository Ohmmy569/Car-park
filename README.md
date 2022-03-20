//name
String name_park1;
String name_park2;
String name_park3;



int trigPin1 = D3;
int echoPin1 = D0;

int trigPin2 = D5;
int echoPin2 = D6;

int trigPin3 = D7;
int echoPin3 = D8;

//nnumber for dis
int st1;
int st2;
int st3;


#if defined(ESP32)
#include <WiFi.h>
#include <FirebaseESP32.h>
#elif defined(ESP8266)
#include <ESP8266WiFi.h>
#include <FirebaseESP8266.h>
#include <LiquidCrystal_I2C.h>
#endif

//Provide the token generation process info.
#include <addons/TokenHelper.h>

//Provide the RTDB payload printing info and other helper functions.
#include <addons/RTDBHelper.h>

/* 1. Define the WiFi credentials */
#define WIFI_SSID "pornphimon_2.4G" 
#define WIFI_PASSWORD "0808321827"

//For the following credentials, see examples/Authentications/SignInAsUser/EmailPassword/EmailPassword.ino

/* 2. Define the API Key */
#define API_KEY "APIของผมเอง"

/* 3. Define the RTDB URL */
#define DATABASE_URL "firebase url ของผม" //<databaseName>.firebaseio.com or <databaseName>.<region>.firebasedatabase.app

/* 4. Define the user Email and password that alreadey registerd or added in your project */
#define USER_EMAIL "เมล auth ใน firebase"
#define USER_PASSWORD "รหัส auth ใน firebase"

//Define Firebase Data object
FirebaseData fbdo;

FirebaseAuth auth;
FirebaseConfig config;

unsigned long sendDataPrevMillis = 0;

unsigned long count = 0;

LiquidCrystal_I2C lcd(0x27, 20, 4);

void setup()
{
  pinMode(trigPin1, OUTPUT);
  pinMode(echoPin1, INPUT);
  pinMode(trigPin2, OUTPUT);
  pinMode(echoPin2, INPUT);
  pinMode(trigPin3, OUTPUT);
  pinMode(echoPin3, INPUT);


  lcd.init();
  lcd.backlight();

  Serial.begin(115200);

  WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
  Serial.print("Connecting to Wi-Fi");
  while (WiFi.status() != WL_CONNECTED)
  {
    Serial.print(".");
    delay(300);
  }
  Serial.println();
  Serial.print("Connected with IP: ");
  Serial.println(WiFi.localIP());
  Serial.println();

  Serial.printf("Firebase Client v%s\n\n", FIREBASE_CLIENT_VERSION);

  /* Assign the api key (required) */
  config.api_key = API_KEY;

  /* Assign the user sign in credentials */
  auth.user.email = USER_EMAIL;
  auth.user.password = USER_PASSWORD;

  /* Assign the RTDB URL (required) */
  config.database_url = DATABASE_URL;

  /* Assign the callback function for the long running token generation task */
  config.token_status_callback = tokenStatusCallback; //see addons/TokenHelper.h

  Firebase.begin(&config, &auth);

  //Comment or pass false value when WiFi reconnection will control by your code or third party library
  Firebase.reconnectWiFi(true);

  Firebase.setDoubleDigits(5);
  
}


void loop()
{
  //get name1
  if (Firebase.getString(fbdo, "name1")) {

    if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_string) {
      name_park1 = fbdo.to<String>();

      Serial.println(name_park1);

    }

  } else {
    Serial.println(fbdo.errorReason());
  }
  delay(2);
  
  //get name2
  if (Firebase.getString(fbdo, "name2")) {

    if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_string) {
      name_park2 = fbdo.to<String>();
      Serial.println(name_park2);

    }

  } else {
    Serial.println(fbdo.errorReason());
  }
  delay(2);ฆ
  
  //get name3
  if (Firebase.getString(fbdo, "name3")) {

    if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_string) {
      name_park3 = fbdo.to<String>();

      Serial.println(name_park3);

    }

  } else {
    Serial.println(fbdo.errorReason());
  }



//outputname
 lcd.setCursor(0, 0);
  lcd.print("----- Car Park -----");
  lcd.setCursor(0, 1);
  lcd.print("Car Ch 1 :");
  lcd.setCursor(0, 2);
  lcd.print("Car Ch 2 :");
  lcd.setCursor(0, 3);
  lcd.print("Car Ch 3 :");
  //Seee
  lcd.setCursor(11, 1);
  lcd.print("         ");
  lcd.setCursor(11, 1);
  lcd.print(name_park1);
  lcd.setCursor(11, 2);
  lcd.print("         ");
  lcd.setCursor(11, 2);
  lcd.print(name_park2);
  lcd.setCursor(11, 3);
  lcd.print("         ");
  lcd.setCursor(11, 3);
  lcd.print(name_park3);
  delay(2);
  
///st1
if (Firebase.getInt(fbdo, "stat1")) {

    if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_integer) {
      st1 = fbdo.to<int>();

  long duration1, distance1;
  digitalWrite(trigPin1, LOW);  // Added this line
  delayMicroseconds(2); // Added this line
  digitalWrite(trigPin1, HIGH);
  delayMicroseconds(10); // Added this line
  digitalWrite(trigPin1, LOW);
  duration1 = pulseIn(echoPin1, HIGH);
  distance1 = (duration1 / 2) / 29.1;


  if (st1 == 0){  
  
    if (14 > distance1)
    {
      st1 = 1;
       Firebase.setInt(fbdo, F("stat1"), st1);
    }
//    else {
//      st1 = 0;
//       Firebase.setInt(fbdo, F("stat1"), st1);
//    }
  }
  else if (st1 == 1){

    if (14 < distance1)
    {
      st1 = 0;
       Firebase.setInt(fbdo, F("stat1"), st1);
    }
//    else {
//      st1 = 1;
//       Firebase.setInt(fbdo, F("stat1"), st1);
//    }
  }
  else{
    if (14 > distance1)
    {
      st1 = 1;
       Firebase.setInt(fbdo, F("stat1"), st1);
    }
  }
    Serial.print("S1 Status1 ");
    Serial.println(st1);
    Serial.println(distance1);
 
  }
    }

else {
    Serial.println(fbdo.errorReason());
  }
  delay(1);
  
///st2
if (Firebase.getInt(fbdo, "stat2")) {

    if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_integer) {
      st2 = fbdo.to<int>();

  long duration2, distance2;
  digitalWrite(trigPin2, LOW);  // Added this line
  delayMicroseconds(2); // Added this line
  digitalWrite(trigPin2, HIGH);
  delayMicroseconds(10); // Added this line
  digitalWrite(trigPin2, LOW);
  duration2 = pulseIn(echoPin2, HIGH);
  distance2 = (duration2 / 2) / 29.1;


  if (distance2 >= 500 || distance2 <= 0) {
    Serial.println("Out of range");
  }
  else {
  if (st2 == 0){  
  
    if (14 > distance2)
    {
      st2 = 1;
       Firebase.setInt(fbdo, F("stat2"), st2);
    }
//    else {
//      st2 = 0;
//       Firebase.setInt(fbdo, F("stat2"), st2);
//    }
  }
  else if (st2 == 1){

    if (14 < distance2)
    {
      st2 = 0;
       Firebase.setInt(fbdo, F("stat2"), st2);
    }
//    else {
//      st2 = 1;
//       Firebase.setInt(fbdo, F("stat2"), st2);
//    }
  }
  else{
    if (14 > distance2)
    {
      st2 = 1;
       Firebase.setInt(fbdo, F("stat2"), st2);
    }
  }
    Serial.print("S2 Status2 ");
    Serial.println(st2);
 
  }
    }
}
else {
    Serial.println(fbdo.errorReason());
  }
  delay(1);
  
  ///st3
if (Firebase.getInt(fbdo, "stat3")) {

    if (fbdo.dataTypeEnum() == fb_esp_rtdb_data_type_integer) {
      st3 = fbdo.to<int>();

  long duration3, distance3;
  digitalWrite(trigPin3, LOW);  // Added this line
  delayMicroseconds(2); // Added this line
  digitalWrite(trigPin3, HIGH);
  delayMicroseconds(10); // Added this line
  digitalWrite(trigPin3, LOW);
  duration3 = pulseIn(echoPin3, HIGH);
  distance3 = (duration3 / 2) / 29.1;


  if (distance3 >= 500 || distance3 <= 0) {
    Serial.println("Out of range");
  }
  else {
  if (st3 == 0){  
  
    if (14 > distance3)
    {
      st3 = 1;
       Firebase.setInt(fbdo, F("stat3"), st3);
    }
//    else {
//      st3 = 0;
//       Firebase.setInt(fbdo, F("stat3"), st3);
//    }
  }
  else if (st3 == 1){

    if (14 < distance3)
    {
      st3 = 0;
       Firebase.setInt(fbdo, F("stat3"), st3);
    }
//    else {
//      st3 = 1;
//       Firebase.setInt(fbdo, F("stat3"), st3);
//    }
  }
  else{
    if (14 > distance3)
    {
      st3 = 1;
       Firebase.setInt(fbdo, F("stat3"), st3);
    }
  }
   
 
  }
   Serial.print("Status3 ");
    Serial.println(st3);
    
    }
    
}
else {
    Serial.println(fbdo.errorReason());

  }
  delay(1);

  
}
