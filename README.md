# nRF-ESP32
Use BLE on mobile to control the ESP32 

<h1 class="auto-style1">Bluetooth BLE with Nordic nRF chipsets&nbsp; </h1>
<p class="auto-style1">&lt;-- <a href="../index.html">Home</a> </p>
<p class="auto-style1">Strings of data can be transferred between
computers by wire or by air. <br />
Serial communication is an long proven solid method 
to send small amounts of data, like instructions or records from measurement on 
a remote device.<br />
An universal asynchronous 
receiver-transmitter&nbsp;(UART) is a&nbsp;computer hardware&nbsp;device for&nbsp;<a href="https://en.wikipedia.org/wiki/Asynchronous_serial_communication">asynchronous 
serial communication</a>&nbsp;in which the data format and transmission speeds are 
configurable as Wikipedia describes it.</p>
<p class="auto-style1">The serial port on a PC can be connected by wire to an 
other PC's serial port. <br />
If from both wires the TX (transmit) pin from one PC is connected 
to the RX (receive) pin of the other PC, the PC's can communicate. <br />
Both PC's must be set to the same transmission speed, the baud rate. The baud 
rate is how many bits per second are send over the wires.
</p>
<p class="auto-style1">A standard communication speed is 9600 baud = 9600 bits 
per second. In this string of bits 
some are control bits. 9600 baud is roughly 1000 bytes per second. <br />
A baud rate of 
115200 is also a common used speed.</p>
<p class="auto-style1">There are several methods that can be used to send 
data through the air.<br />
ESP32 and some Arduino MCU's use the Nordic nRF52 chipsets. They are often 
integrated in the processor boards together with an WIFI module.<br />
<br />
Bluetooth has a range of 10 meters and can be transmitted and received with a 
mobile phone. <br />
There are similar serial radio modules, known as HC-12, that has ranges up to 1000 
meter. (<a href="../Woordklok/DCFNanoEvery/DCFHC12Transmitter.html">See an 
example here</a>) <br />
Two HC-12 modules can be used instead of a long cable between two devices. They 
are easy to operate but you can not use a phone.&nbsp; <br />
A HM-10 or similar BLE module, like JDY-23, can be used with Android and Apple 
phones. HC-0x modules, like the popular HC-05, can not be used with Apple 
devices. Therefore HM-10 modules are a good choice.</p>
<p class="auto-style1">This page describes how to use a phone to communicate 
with an ESP32 program to send instructions and receive results.</p>
<p class="auto-style1"><span class="auto-style3"><strong>The menu to display in the terminal</strong></span> </p>
<p class="auto-style1">
<img alt="nRF52 menu" height="450" src="Pics/nrfmenu.png" /></p>
<p class="auto-style1">
The menu is a character array with text to be displayed.<br />
In this example a menu line can not be longer than 40 characters.</p>
<p class="auto-style1">
When you send the character i the menu is displayed in the app.<br />
&nbsp;<br />
</p>
<pre>//--------------------------------------------
// Menu
//0        1         2         3         4
//1234567890123456789012345678901234567890----  
 char menu[][40] = 
 {
 "A SSID", 
 "B Password",
 "C BLE beacon name",
 "I Print this menu",
 "R Reset settings"
 };</pre>
<p class="auto-style1">
<br />
<br />
</p>
<p class="auto-style1">
The subroutine below translates the received command line to an action;a 
subroutine to be executed.</p>
<pre>//--------------------------------------------
//  CLOCK Input from Bluetooth or Serial
//--------------------------------------------
void ReworkInputString(String InputString)
{
 char ff[50];  InputString.toCharArray(ff,InputString.length());                              // Convert a String to char array
 sprintf(sptext,"Inputstring: %s  Lengte : %d\n", ff,InputString.length()-1); 
 // Tekstprint(sptext);
 if(InputString.length()&gt; 40){Serial.printf("Input string too long (max40)\n"); return;}                                                         // If garbage return
 sptext[0] = 0;                                                                               // Empty the sptext string
 if(InputString[0] &gt; 31 &amp;&amp; InputString[0] &lt;127)                                               // Does the string start with a letter?
  { 
  switch (InputString[0])
   {
    case 'A':
    case 'a': 
            if (InputString.length() &gt;5 )
            {
             InputString.substring(1).toCharArray(Mem.Ssid,InputString.length()-1);
             sprintf(sptext,"SSID set: %s", Mem.Ssid);  
            }
            else sprintf(sptext,"**** Length fault. Use between 4 and 30 characters ****");
            break;
    case 'B':
    case 'b': 
           if (InputString.length() &gt;5 )
            {  
             InputString.substring(1).toCharArray(Mem.Password,InputString.length()-1);
             sprintf(sptext,"Password set: %s\n Enter @ to reset ESP32 and connect to WIFI and NTP", Mem.Password); 
            }
            else sprintf(sptext,"%s,**** Length fault. Use between 4 and 40 characters ****",Mem.Password);
            break;   
    case 'C':
    case 'c': 
           if (InputString.length() &gt;5 )
            {  
             InputString.substring(1).toCharArray(Mem.BLEbroadcastName,InputString.length()-1);
             sprintf(sptext,"BLE broadcast name set: %s", Mem.BLEbroadcastName); 
            }
            else sprintf(sptext,"**** Length fault. Use between 4 and 30 characters ****");
            break;
    case 'I':
    case 'i': 
            SWversion();
            break;
    case 'R':
    case 'r':
             if (InputString.length() == 2)
               {   
                Reset();
                sprintf(sptext,"\nReset to default values: Done");
               }                                
             else sprintf(sptext,"**** Length fault. Enter R ****");
             break;      
 
    default: break;
    }
  }  
 Tekstprintln(sptext);    
 StoreStructInFlashMemory();                         
 InputString = "";
}</pre>

<class="auto-style1"><span class="auto-style1">A working INO file for an Arduino can be found at the 
bottom of this page and as file here: <a href="BLEHM-10Menu/BLEHM-10Menu.ino">BLEHM-10Menu.ino</a><br /><br />
if the menu program is used in nRF equipped MCU's like the RP2040 Connect or 
ESP32 variants use the <a href="https://apps.apple.com/nl/app/bleserial-nrf/id1632235163">free IOS nRF</a> app.
</span>
<p class="auto-style1">The priced app<span class="auto-style1"> <a href="IOSappMain.html">BLEserial Pro app</a>. 
has more capabilities and connect to both CC25xx and nRF chipsets.<br />
<br />
</span>&nbsp;<span class="auto-style1"><br />
</span>&nbsp;<span class="auto-style1"><a href="https://apps.apple.com/nl/app/bleserial-nrf/id1632235163"><img alt="BLEserial in app store" class="auto-style4" height="632" src="Pics/NrfApp.png" /></a>&nbsp;&nbsp;&nbsp;
<a href="Pics/terminalIOS.PNG">
<img alt="Terminal windows" class="auto-style4" height="632" src="Pics/terminalIOS.PNG" width="400" /></a><br />
</span>&nbsp;<span class="auto-style1"><br />
<br />
</span>
<a href="https://apps.apple.com/nl/app/bleserial-hm-10/id1602239700?l=en"></a>
</p>
<p class="auto-style1"><br />
<br />
</p>
<pre>// =============================================================================================================================
//--------------------------------------------
// ESP32-C3 /S3 Includes defines and initialisations
//--------------------------------------------

#include &lt;NimBLEDevice.h&gt;      // For BLE communication  https://github.com/h2zero/NimBLE-Arduino
#include &lt;Preferences.h&gt;

Preferences FLASHSTOR;
static  uint32_t msTick;                        // Number of millisecond ticks since we last incremented the second counter

//--------------------------------------------
// BLE   //#include &lt;NimBLEDevice.h&gt;
//--------------------------------------------
BLEServer *pServer = NULL;
BLECharacteristic * pTxCharacteristic;
bool deviceConnected    = false;
bool oldDeviceConnected = false;
std::string ReceivedMessageBLE;

#define SERVICE_UUID           "6E400001-B5A3-F393-E0A9-E50E24DCCA9E"        // UART service UUID
#define CHARACTERISTIC_UUID_RX "6E400002-B5A3-F393-E0A9-E50E24DCCA9E"
#define CHARACTERISTIC_UUID_TX "6E400003-B5A3-F393-E0A9-E50E24DCCA9E"

//----------------------------------------
// Common
//----------------------------------------

char sptext[120];                        // For common print use 
struct EEPROMstorage {                   // Data storage in EEPROM to maintain them after power loss
  char Ssid[30];
  char Password[40];
  char BLEbroadcastName[30];             // Name of the BLE beacon
  byte UseBLELongString = 0;             // Send strings longer than 20 bytes per message. Possible in IOS app BLEserial Pro 
  int  Checksum         = 0;
}  Mem; 

//--------------------------------------------
// Menu
//0        1         2         3         4
//1234567890123456789012345678901234567890----  
 char menu[][40] = 
 {
 "A SSID", 
 "B Password",
 "C BLE beacon name",
 "I Print this menu",
 "R Reset settings"
 };
 
//  -------------------------------------   End Definitions  ---------------------------------------
//                                                                                            //

//--------------------------------------------
// ARDUINO Setup
//--------------------------------------------
void setup() 
{
 Serial.begin(115200);  Tekstprintln("Serial started");                                       // Setup the serial port to 115200 baud //  
 InitStorage();         Tekstprintln("Setting loaded");                                       // Load settings from storage and check validity 
 StartBLEService();     Tekstprintln("BLE started");                                           // Start BLE service                                                                                             // Print the tekst time in the display 
 SWversion();
}
//                                                                                            //
//--------------------------------------------
// ARDUINO Loop
//--------------------------------------------
void loop() 
{
 EverySecondCheck();  
 CheckDevices();
}
//--------------------------------------------
// Common Check connected input devices
//--------------------------------------------
void CheckDevices(void)
{
 CheckBLE();                                                                                  // Something with BLE to do?
 SerialCheck();                                                                               // Check serial port every second 
}
//                                                                                            //
//--------------------------------------------
// CLOCK Update routine to run something every second
//--------------------------------------------
void EverySecondCheck(void)
{
 uint32_t msLeap = millis() - msTick;
 if (msLeap &gt;999)                                                                             // Every second enter the loop
  { 
   Serial.println(millis());
   msTick = millis();
  }  
 }

//--------------------------------------------
// Common check for serial input
//--------------------------------------------
void SerialCheck(void)
{
 String SerialString; 
 while (Serial.available())
    { 
     char c = Serial.read();                                                               // Serial.write(c);
     if (c&gt;31 &amp;&amp; c&lt;128) SerialString += c;                                                    // Allow input from Space - Del
     else c = 0;                                                                              // Delete a CR
    }
 if (SerialString.length()&gt;0) 
    {
     ReworkInputString(SerialString+"\n");                                                    // Rework ReworkInputString();
     SerialString = "";
    }
}

//--------------------------------------------
// Common common print routines
//--------------------------------------------
void Tekstprint(char const *tekst)    { if(Serial) Serial.print(tekst);  SendMessageBLE(tekst);sptext[0]=0;   } 
void Tekstprintln(char const *tekst)  { sprintf(sptext,"%s\n",tekst); Tekstprint(sptext);  }
void TekstSprint(char const *tekst)   { printf(tekst); sptext[0]=0;}                          // printing for Debugging purposes in serial monitor 
void TekstSprintln(char const *tekst) { sprintf(sptext,"%s\n",tekst); TekstSprint(sptext); }

//--------------------------------------------
//  CLOCK Input from Bluetooth or Serial
//--------------------------------------------
void ReworkInputString(String InputString)
{
 char ff[50];  InputString.toCharArray(ff,InputString.length());                              // Convert a String to char array
 sprintf(sptext,"Inputstring: %s  Lengte : %d\n", ff,InputString.length()-1); 
 // Tekstprint(sptext);
 if(InputString.length()&gt; 40){Serial.printf("Input string too long (max40)\n"); return;}                                                         // If garbage return
 sptext[0] = 0;                                                                               // Empty the sptext string
 if(InputString[0] &gt; 31 &amp;&amp; InputString[0] &lt;127)                                               // Does the string start with a letter?
  { 
  switch (InputString[0])
   {
    case 'A':
    case 'a': 
            if (InputString.length() &gt;5 )
            {
             InputString.substring(1).toCharArray(Mem.Ssid,InputString.length()-1);
             sprintf(sptext,"SSID set: %s", Mem.Ssid);  
            }
            else sprintf(sptext,"**** Length fault. Use between 4 and 30 characters ****");
            break;
    case 'B':
    case 'b': 
           if (InputString.length() &gt;5 )
            {  
             InputString.substring(1).toCharArray(Mem.Password,InputString.length()-1);
             sprintf(sptext,"Password set: %s\n Enter @ to reset ESP32 and connect to WIFI and NTP", Mem.Password); 
            }
            else sprintf(sptext,"%s,**** Length fault. Use between 4 and 40 characters ****",Mem.Password);
            break;   
    case 'C':
    case 'c': 
           if (InputString.length() &gt;5 )
            {  
             InputString.substring(1).toCharArray(Mem.BLEbroadcastName,InputString.length()-1);
             sprintf(sptext,"BLE broadcast name set: %s", Mem.BLEbroadcastName); 
            }
            else sprintf(sptext,"**** Length fault. Use between 4 and 30 characters ****");
            break;
    case 'I':
    case 'i': 
            SWversion();
            break;
    case 'R':
    case 'r':
             if (InputString.length() == 2)
               {   
                Reset();
                sprintf(sptext,"\nReset to default values: Done");
               }                                
             else sprintf(sptext,"**** Length fault. Enter R ****");
             break;      
 
    default: break;
    }
  }  
 Tekstprintln(sptext);    
 StoreStructInFlashMemory();                         
 InputString = "";
}

//--------------------------------------------
// Print Menu and Version info
//--------------------------------------------
void SWversion(void) 
{ 
 #define FILENAAM (strrchr(__FILE__, '\\') ? strrchr(__FILE__, '\\') + 1 : __FILE__)
 PrintLine(35);
 for (uint8_t i = 0; i &lt; sizeof(menu) / sizeof(menu[0]); Tekstprintln(menu[i++]));
 sprintf(sptext,"SSID: %s", Mem.Ssid);                                           Tekstprintln(sptext);
// sprintf(sptext,"Password: %s", Mem.Password);                                       Tekstprintln(sptext);
 sprintf(sptext,"BLE name: %s", Mem.BLEbroadcastName);                               Tekstprintln(sptext);
 sprintf(sptext,"%s",Mem.UseBLELongString? "FastBLE=On":"FastBLE=Off" );           Tekstprintln(sptext);
 sprintf(sptext,"Software: %s",FILENAAM);                                                    Tekstprintln(sptext);  //VERSION); 
 PrintLine(35);
}
void PrintLine(byte Lengte)
{
 for(int n=0; n&lt;Lengte; n++) sptext[n]='_';
 sptext[Lengte] = 0;
 Tekstprintln(sptext);
}
//-----------------------------
// BLE 
// SendMessage by BLE Slow in packets of 20 chars or fast in one long string.
// Fast can be used in IOS app BLESerial Pro
//------------------------------
void SendMessageBLE(std::string Message)
{
 if(deviceConnected) 
   {
    if (Mem.UseBLELongString)                                                                 // If Fast transmission is possible
     {
      pTxCharacteristic-&gt;setValue(Message); 
      pTxCharacteristic-&gt;notify();
      delay(10);                                                                              // Bluetooth stack will go into congestion, if too many packets are sent
     } 
   else                                                                                      // Packets of max 20 bytes
     {   
      int parts = (Message.length()/20) + 1;
      for(int n=0;n&lt;parts;n++)
        {   
         pTxCharacteristic-&gt;setValue(Message.substr(n*20, 20)); 
         pTxCharacteristic-&gt;notify();
         delay(40);                                                                           // Bluetooth stack will go into congestion, if too many packets are sent
        }
     }
   } 
}

//-----------------------------
// BLE Start BLE Classes
//------------------------------
class MyServerCallbacks: public BLEServerCallbacks 
{
 void onConnect(BLEServer* pServer) {deviceConnected = true; };
 void onDisconnect(BLEServer* pServer) {deviceConnected = false;}
};

class MyCallbacks: public BLECharacteristicCallbacks 
{
 void onWrite(BLECharacteristic *pCharacteristic) 
  {
   std::string rxValue = pCharacteristic-&gt;getValue();
   ReceivedMessageBLE = rxValue + "\n";
//   if (rxValue.length() &gt; 0) {for (int i = 0; i &lt; rxValue.length(); i++) printf("%c",rxValue[i]); }
//   printf("\n");
  }  
};

//-----------------------------
// BLE Start BLE Service
//------------------------------
void StartBLEService(void)
{
 BLEDevice::init(Mem.BLEbroadcastName);                                                          // Create the BLE Device
 pServer = BLEDevice::createServer();                                                         // Create the BLE Server
 pServer-&gt;setCallbacks(new MyServerCallbacks());
 BLEService *pService = pServer-&gt;createService(SERVICE_UUID);                                 // Create the BLE Service
 pTxCharacteristic                     =                                                      // Create a BLE Characteristic 
     pService-&gt;createCharacteristic(CHARACTERISTIC_UUID_TX, NIMBLE_PROPERTY::NOTIFY);                 
 BLECharacteristic * pRxCharacteristic = 
     pService-&gt;createCharacteristic(CHARACTERISTIC_UUID_RX, NIMBLE_PROPERTY::WRITE);
 pRxCharacteristic-&gt;setCallbacks(new MyCallbacks());
 pService-&gt;start(); 
 BLEAdvertising *pAdvertising = BLEDevice::getAdvertising();
 pAdvertising-&gt;addServiceUUID(SERVICE_UUID); 
 pServer-&gt;start();                                                                            // Start the server  
 pServer-&gt;getAdvertising()-&gt;start();                                                          // Start advertising
 TekstSprint("BLE Waiting a client connection to notify ...\n"); 
}
//                                                                                            //
//-----------------------------
// BLE  CheckBLE
//------------------------------
void CheckBLE(void)
{
 if(!deviceConnected &amp;&amp; oldDeviceConnected)                                                   // Disconnecting
   {
    delay(300);                                                                               // Give the bluetooth stack the chance to get things ready
    pServer-&gt;startAdvertising();                                                              // Restart advertising
    TekstSprint("Start advertising\n");
    oldDeviceConnected = deviceConnected;
   }
 if(deviceConnected &amp;&amp; !oldDeviceConnected)                                                   // Connecting
   { 
    oldDeviceConnected = deviceConnected;
    SWversion();
   }
 if(ReceivedMessageBLE.length()&gt;0)
   {
    SendMessageBLE(ReceivedMessageBLE);
    String BLEtext = ReceivedMessageBLE.c_str();
    ReceivedMessageBLE = "";
    ReworkInputString(BLEtext); 
   }
}
//                                                                                            //

//--------------------------------------------
// Common Init and check contents of EEPROM
//--------------------------------------------
void InitStorage(void)
{
 GetStructFromFlashMemory();
 if( Mem.Checksum != 25065)
   {
    sprintf(sptext,"Checksum (25065) invalid: %d\n Resetting to default values",Mem.Checksum); 
    Tekstprintln(sptext); 
    Reset();                                                                                  // If the checksum is NOK the Settings were not set
   }
 StoreStructInFlashMemory();
}
//--------------------------------------------
// COMMON Store mem.struct in FlashStorage or SD
// Preferences.h  
//--------------------------------------------
void StoreStructInFlashMemory(void)
{
 FLASHSTOR.begin("Mem",false);       //  delay(100);
 FLASHSTOR.putBytes("Mem", &amp;Mem , sizeof(Mem) );
 FLASHSTOR.end();          
 }
//--------------------------------------------
// COMMON Get data from FlashStorage
// Preferences.h
//--------------------------------------------
void GetStructFromFlashMemory(void)
{
 FLASHSTOR.begin("Mem", false);
 FLASHSTOR.getBytes("Mem", &amp;Mem, sizeof(Mem) );
 FLASHSTOR.end();         
 sprintf(sptext,"Mem.Checksum = %d",Mem.Checksum);Tekstprintln(sptext); 
}

//------------------------------------------------------------------------------
// Common Reset to default settings
//------------------------------------------------------------------------------
void Reset(void)
{
 Mem.Checksum         = 25065;                                                                 //
 Mem.UseBLELongString = 0;
 strcpy(Mem.Ssid,"MySSID");                                                                         // Default SSID
 strcpy(Mem.Password,"MyPass");                                                                     // Default password
 strcpy(Mem.BLEbroadcastName,"ESP32Menu");
 Tekstprintln("**** Reset of preferences ****"); 
 StoreStructInFlashMemory();                                                                  // Update Mem struct       
 SWversion();                                                                                 // Display the version number of the software
}</pre>
<p class="auto-style1">
<br />
&nbsp;<br />
</p>
<p class="auto-style1">&nbsp;</p>

