Нижний уровень Agrolab multibot
-------------------------------

Этот код написан для платформы Arduino и использует некоторые дополнительные библиотеки для управления устройством Agrolab multibot с задачами, связанными с перемещением, измерением температуры, влажности и другими функциями. Давайте разберём каждую часть кода:

1. Подключаемые библиотеки:

- "RTOS.h": библиотека для использования операционной системы реального времени (RTOS) - позволяет системе выполнять многозадачность.

- <AccelStepper.h>: библиотека для управления шаговыми двигателями с акселерацией и декселерацией.

- "DHT.h": библиотека для работы с датчиками влажности и температуры DHT.

2. Подключаемые страницы кода: содержат дополнительные настройки, функции и переменные, используемые в главном скетче.

3. Объявление потоков: создание двух потоков - один для вывода информации (dataThread), другой для отправления команд в систему (commandsThread).

4. Функция setup():

- Запуск серийной связи с скоростью 1000000.

- Инициализация датчика DHT.

- Вызов функции 'funkSetup' для задания начальных значений и настроек.

- Создание потоков и запуск ядра RTOS.

- Функция getValue(): вспомогательная функция для извлечения значения из строки с заданным разделителем на заданной позиции.

5. Функция data(): функция потока данных, выводит информацию с датчиков и позицию шаговых двигателей. В случае экстренной остановки ожидает серийное число 666.

7. Функция commands(): функция потока команд, ожидает ввод команд через последовательный монитор. Команды выполняют различные функции, такие как перемещение, управление шаговыми двигателями, использование лазера или насоса и другие. При получении команды "p" и координат x, y, z, программа перемещает двигатели на указанные позиции.

Код реализует управление устройством Agrolab multibot, выполняя различные функции, такие как перемещение, сбор данных, управление светом, насосом, лазером, перемещение и управление шаговыми двигателями.

::

    //Главная страница программы
    //===========================================================================================
    //Подключаемые библиотеки
    #include "RTOS.h"
    #include <AccelStepper.h>
    #include "DHT.h"
    //============================================================================================
    //подключаемые страницы кода
    #include "Setup.h"
    #include "Steppers.h"
    #include "funkSetup.h"
    #include "addFunk.h"
    #include "work.h"
    //=============================================================================================
    //объявление потоков
    osThreadId thread_0_dataThread;       //поток для вывода информации
    osThreadId thread_0_commandsThread;   //поток для отправления команд в систему
    //==============================================================================================
    void setup() {
      Serial.begin(1000000);
      dht.begin();                        //запуск dht датчика                      
      funkSetup(); 
      /*delay(10000);
      enON();
      homeZ();
      homeY();
      homeX();
    
      moveX(X_MAX/2+500);
      moveY(Y_MAX/2+350);
      moveZ(Z_MAX-250);
      
      use_lazer();
      use_lazer();
      homeZ();
      homeY();
      homeX();
      */
      osThreadDef(DATA_THREAD, data, osPriorityNormal, 0, 1024) ;                  //описывание аргументы потока с данными
      osThreadDef(commandsThread,  commands,  osPriorityNormal, 0, 1024);          //описывание аргументы потока с командами
      
      thread_0_dataThread = osThreadCreate(osThread(DATA_THREAD), NULL);           //создание потока с данными
      thread_0_commandsThread  = osThreadCreate(osThread(commandsThread), NULL);   //создание потока с командами
      
      osKernelStart();   //запуск ядра RTOS для инициализации потоков
    
    }
    
    //=======================================================================
    //Функция создания разделителя для корректного вывода данных
    String getValue(String data, char separator, int index)
    {
        int found = 0;
        int strIndex[] = { 0, -1 };
        int maxIndex = data.length() - 1;
    
        for (int i = 0; i <= maxIndex && found <= index; i++) {
            if (data.charAt(i) == separator || i == maxIndex) {
                found++;
                strIndex[0] = strIndex[1] + 1;
                strIndex[1] = (i == maxIndex) ? i+1 : i;
            }
        }
        return found > index ? data.substring(strIndex[0], strIndex[1]) : "";
    }
    //=======================================================================
    //Функция для потока данных
    static void data(void const *argument)
    {   
      (void) argument;    
        
      for(;;)
        {                     
        Serial.print(stepperX.currentPosition());     //Вывод положения шаговика по Ох
        Serial.print(";");
        Serial.print(stepperY.currentPosition());     //Вывод положения шаговика по Оy
        Serial.print(";");
        Serial.print(stepperZ.currentPosition());     //Вывод положения шаговика по Оz
        Serial.print(";");
        Serial.print(dht.readTemperature());          //Вывод температуры с датчика DHT
        Serial.print(";");
        Serial.print(dht.readHumidity());             //Вывода уровня влажности с датчика DHT
        Serial.print(";");
        Serial.print(map(analogRead(SoilTest), 0, 1023, 0, 100)); //Вывода уровня влажности почвы с резистивного датчика влажности почвы
        Serial.print(";");
        Serial.print(0);                              //Вывод уровня освещённости
        Serial.print(";");
        int PercInt = Perc;
        if (PercInt == 99)
            Perc = 100;
        Serial.println(PercInt);                      //Вывод процента выполненной работы
      
        if (Serial.available())                       //Функция экстренной остановки
          {
            int var = Serial.parseInt();
            if (var==666)
            {
              Perc = -1;
              force_stop=true;
    
              Serial.print(stepperX.currentPosition());     //Вывод положения шаговика по Ох
              Serial.print(";");
              Serial.print(stepperY.currentPosition());     //Вывод положения шаговика по Оy
              Serial.print(";");
              Serial.print(stepperZ.currentPosition());     //Вывод положения шаговика по Оz
              Serial.print(";");
              Serial.print(dht.readTemperature());          //Вывод температуры с датчика DHT
              Serial.print(";");
              Serial.print(dht.readHumidity());             //Вывода уровня влажности с датчика DHT
              Serial.print(";");
              Serial.print(map(analogRead(SoilTest), 0, 1023, 0, 100)); //Вывода уровня влажности почвы с резистивного датчика влажности почвы
              Serial.print(";");
              Serial.print(0);                              //Вывод уровня освещённости
              Serial.print(";");
    //          int PercInt = Perc;
              Serial.println(666);                      //Вывод процента выполненной работы
              Perc = 0;
              
              while(!stopped)
              {
              enOFF();
              
              }
              force_stop=false;
              stopped = false;
            }
          }
          
        osDelay(150);     //задержка потока
        }
    }
    //=======================================================================
    static void commands(void const *argument)
    {
      (void) argument;
      for(;;)
      { 
        /*                      
        if (millis()- light_time > 30000 && light_enabled)
        {
          toggle_light();
          light_enabled=false;
        }
        */
        if (Serial.available())
        {
          serial_command = Serial.readStringUntil('\n');
          if (serial_command[0]=='p' && serial_command[1]==' ')
          {
            String sx,sy,sz;
            sx = getValue(serial_command, ' ', 1);
            sy = getValue(serial_command, ' ', 2);
            sz = getValue(serial_command, ' ', 3);
      
            int x,y,z;
            x = ::atof(sx.c_str());
            y = ::atof(sy.c_str());
            z = ::atof(sz.c_str());
            
            moveX(x);
            moveY(y);
            moveZ(z);
          }
          else
          {
            //возможно понадобится делать отдельный поток, но нужно организовать сброс команды по отдельной команде 
            //int var = Serial.parseInt();
            int var = ::atof(serial_command.c_str());
            switch (var) {
              case 1:
                enON();
                homeALL();
                water();
                Perc = 0;
                break;
              case 2:
                enON();
                homeZ();
                homeY();
                homeX();
                laser();
                Perc = 0;
                break;
              case 3:
                enON();
                homeALL();
                gather();
                Perc = 0;
                break;
              case 4:
                enON();
                homeALL();
                drill();
                Perc = 0;
                break;
              case 5:
                enON();
                homeALL();
                break;
              case 6:
                enOFF();
                break;
              case 7:
                enON();
                break;
              case 8:
                enON();
                homeALL();
                fork();
                Perc = 0;
                break;
              case 9:
                toggle_light();
                break;
              case 10:
                enON();
                homeX();
                break;
              case 11:
                enON();
                homeY();
                break;
              case 12:
                enON();
                homeZ();
                break;
              case 13:
                enON();
                homeALL();
                SeedGather();
                Perc = 0;
                break;
              case 14:
                enON();
                toggle_pump();
                break;
              case 15:
                enON();
                lazer_on();
                break;
              case 16:
                enON();
                lazer_off();
                break;
            }
          }
        }
        osDelay(10); 
      }
    }
    //=======================================================================
