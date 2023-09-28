Полная программа нижнего уровня Agrolab Multibot
------------------------------------------------

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
        <iframe src="https://www.youtube.com/embed/3iWLTzlO0gs?si=vRDUmwRxwti0IDAA" frameborder="0" allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;"></iframe>
    </div>

Этот код написан для платформы Arduino и использует некоторые дополнительные библиотеки для управления устройством Agrolab multibot с задачами, связанными с перемещением, измерением температуры, влажности и другими функциями. Давайте разберём каждую часть кода:

Подключаемые библиотеки
~~~~~~~~~~~~~~~~~~~~~~~

- ``"RTOS.h"``: библиотека для использования операционной системы реального времени (RTOS) - позволяет системе выполнять многозадачность.

- ``<AccelStepper.h>``: библиотека для управления шаговыми двигателями с акселерацией и декселерацией.

- ``"DHT.h"``: библиотека для работы с датчиками влажности и температуры DHT.

Подключаемые страницы кода
~~~~~~~~~~~~~~~~~~~~~~~~~~

Содержат дополнительные настройки, функции и переменные, используемые в главном скетче.

1. "Setup.h"

Код определяет константы и переменные для различных входных/выходных контактов (пинов) и настроек.

Перечислим некоторые из них:

- Константы, определяющие пины для подключения степперных моторов (X, Y и Z).

- Константы, определения максимальных значений координат (X_MAX, Y_MAX, Z_MAX) для каждого из степперных моторов.

- Определение пинов для датчиков концевых выключателей (X_ENDSTOP1, Y_ENDSTOP1, Z_ENDSTOP1).

- Определение пинов для различных светодиодов (ULED1-4).

- Определение аналоговых пинов для сенсоров (analogPin1, analogPin2, analogPin3).

- Определение пина для чтения показаний влажности почвы (SoilTest).

- Установка связанных с датчиками DHT для измерения температуры и влажности (DHTPIN, DHTTYPE).

- Определение значений ШПУ для различных рабочих операций (DRILL_Z, WATER_Z, FORK_Z, LAZER_Z, COLLECT_Z).

- Установка пинов для управления насосом, захватом, светом и другими дополнительными устройствами.

Определение переменных, таких как:

- force_stop и stopped, используемых для реализации аварийной остановки.

- light_time и light_enabled, связанные с временем работы света.

- serial_command, используется для хранения и анализа команд, получаемых через последовательное соединение.

2. "Steppers.h"

- homeZ(): Функция домашней позиции для оси Z. Степпер-мотор перемещается в отрицательном направлении, пока не достигнет концевого выключателя. Если процесс остановлен, функция возвращает значение 1, иначе 0.

- homeX(): Аналогичная функция домашней позиции для оси X.

- homeY(): Аналогичная функция домашней позиции для оси Y.

- homeALL(): Функция домашней позиции для всех осей (X, Y и Z) одновременно. Процесс остановлен, если функция возвращает значение 1, иначе 0.

- moveZ(int distance): Функция перемещения степпер-мотора по оси Z на заданное расстояние. Функция проверяет допустимость расстояния и адаптирует его при необходимости. Если процесс остановлен, функция возвращает значение 1, иначе 0.

- moveX(int distance): Аналогичная функция перемещения степпер-мотора для оси X.

- moveY(int distance): Аналогичная функция перемещения степпер-мотора для оси Y.

- moveXY(int x, int y): Функция для перемещения степпер-моторов X и Y одновременно на заданные координаты x и y. Функция также проверяет допустимость координат и корректирует их при необходимости. Если процесс остановлен, функция возвращает значение 1, иначе 0.

3. funkSetup.h

- Настройка трех шаговых двигателей (stepperX, stepperY, и stepperZ) с заданными максимальной скоростью, текущей скоростью, ускорением и настройкой инвертирования ножек (пинов).

- Установка ножек (пинов) светодиодов (ULED1, ULED2, ULED3, и ULED4) на выход (OUTPUT).

- Установка пина лазера (LAZER) на выход (OUTPUT) и установка его начального значения на 0.

- Установка ножек (пинов) для управления периферийными устройствами (CONTROL_PEREPHERIAL_PIN_SELECT, LIGHT_PIN, и PUMP_PIN) на выход (OUTPUT) и задание начальных значений.

- Установка ножек (пинов) для включения или отключения шаговых двигателей (ENPin1, ENPin2, ENPin3, и ENPin4) на выход (OUTPUT).

- Установка ножек (пинов) шага и направления шаговых двигателей (stepPinX, stepPinY, stepPinZ, dirPinX, dirPinY и dirPinZ) на выход (OUTPUT).

- Установка ножек (пинов) концевых выключателей для каждого из шаговых двигателей (X_ENDSTOP1, Y_ENDSTOP1 и Z_ENDSTOP1) на вход с подтяжкой к питанию (INPUT_PULLUP).

- Установка ножек (пинов) для манипулятора (GRASP_PIN и GRASP_PIN_EN) на выход (OUTPUT).

4. "addFunk.h"

**Функция enOFF**

В этой функции, пины ENPin1, ENPin2, ENPin3 и ENPin4 задаются в состояние HIGH. Это обычно используется для выключения электронных компонентов, подключенных к данным выводам, хотя это зависит от конкретной схемы подключения.

*Функция enON*

В этой функции, пины ENPin1, ENPin2, ENPin3 и ENPin4 задаются в состояние LOW. Это обычно используется для включения электронных компонентов, подключенных к данным выводам, хотя это также зависит от конкретной схемы подключения.

5. "work.h"

Предназначен для управления и тестирования оборудования, использующего шаговые моторы для перемещения по осям X, Y и Z.

Объявление потоков
~~~~~~~~~~~~~~~~~~

Создание двух потоков - один для вывода информации (dataThread), другой для отправления команд в систему (commandsThread).

Функция setup
~~~~~~~~~~~~~

- Запуск серийной связи с скоростью 1000000.

- Инициализация датчика DHT.

- Вызов функции 'funkSetup' для задания начальных значений и настроек.

- Создание потоков и запуск ядра RTOS.

- Функция getValue(): вспомогательная функция для извлечения значения из строки с заданным разделителем на заданной позиции.

Функция data
~~~~~~~~~~~~

Функция потока данных, выводит информацию с датчиков и позицию шаговых двигателей. В случае экстренной остановки ожидает серийное число 666.

Функция commands
~~~~~~~~~~~~~~~~

Функция потока команд, ожидает ввод команд через последовательный монитор. Команды выполняют различные функции, такие как перемещение, управление шаговыми двигателями, использование лазера или насоса и другие. При получении команды "p" и координат x, y, z, программа перемещает двигатели на указанные позиции.

Код реализует управление устройством Agrolab multibot, выполняя различные функции, такие как перемещение, сбор данных, управление светом, насосом, лазером, перемещение и управление шаговыми двигателями.

Код
~~~

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
    


**Подключаемые страницы кода**


::

    //    work.h

    int drill(int rows = 3, int columns = 2)
    {
      moveX(X_MAX);
      moveY(Y_MAX);
      
      Iter = rows * columns;
      WorkingProg = 100 /  Iter;
    
      for (int j = 1; j < columns + 1; j++) {
        for (int i = 1; i < rows + 1; i++) {
          if (force_stop)
          {
            stopped = true;
            return 1;
          }
    
          
          
          moveY(stepperY.currentPosition() - (Y_MAX / (rows + 1)));
          moveZ(DRILL_Z);
          stepperX.setMaxSpeed(500.0);
          stepperX.setSpeed(300);
          stepperX.setAcceleration(80000);
    
          stepperY.setMaxSpeed(500.0);
          stepperY.setSpeed(300);
          stepperY.setAcceleration(80000);
    
          moveY(stepperY.currentPosition() - 50);
          moveX(stepperX.currentPosition() - 50);
          moveY(stepperY.currentPosition() + 100);
          moveX(stepperX.currentPosition() + 100);
          moveY(stepperY.currentPosition() - 50);
          moveX(stepperX.currentPosition() - 50);
    
          stepperX.setMaxSpeed(1000.0*0.58);
          stepperX.setSpeed(500*0.58);
          stepperX.setAcceleration(80000);
        
          stepperY.setMaxSpeed(1000.0*0.58);
          stepperY.setSpeed(500*0.58);
          stepperY.setAcceleration(80000);
    
          moveZ(DRILL_Z - 2300);
    
          Perc = Perc + WorkingProg;
        }
    
        moveX(stepperX.currentPosition() - (X_MAX / (columns + 1)));
        moveY(Y_MAX);
      }
    
      homeZ();
      return 0;
    }
    
    int laser(int rows = 3, int columns = 2)
    {
      moveX(X_MAX);
      moveY(Y_MAX);
      
      Iter = rows * columns;
      WorkingProg = 100 / Iter;
      
      for (int j = 1; j < columns + 1; j++) {
        for (int i = 1; i < rows + 1; i++) {
    
          if (force_stop)
          {
            stopped = true;
            return 1;
          }
    
          
          moveY(stepperY.currentPosition() - (Y_MAX / (rows + 1)));
          moveZ(LAZER_Z);
          analogWrite(LAZER, 255);
          delay(500);
          analogWrite(LAZER, 0);
          moveZ(LAZER_Z - 1500);
    
          Perc = Perc + WorkingProg;
        }
    
        moveX(stepperX.currentPosition() - (X_MAX / (columns + 1)));
        moveY(Y_MAX);
      }
      homeZ();
      return 0;
    }
    
    int water(int rows = 3, int columns = 2)
    {
      moveX(X_MAX);
      moveY(Y_MAX);
      
      Iter = rows * columns;
      WorkingProg = 100 / Iter;
      
      for (int j = 1; j < columns + 1; j++) {
        for (int i = 1; i < rows + 1; i++) {
     
          if (force_stop)
          {
            stopped = true;
            return 1;
          }
     
          moveY(stepperY.currentPosition() - (Y_MAX / (rows + 1)));
          moveZ(WATER_Z);
          if (!force_stop)
          {
            digitalWrite(PUMP_PIN, 1);
            delay(2500);
            digitalWrite(PUMP_PIN, 0);
          }
          moveZ(WATER_Z - 1500);
    
          Perc = Perc + WorkingProg;
        }
    
        moveX(stepperX.currentPosition() - (X_MAX / (columns + 1)));
        moveY(Y_MAX);
      }
      homeZ();
      return 0;
    }
    
    int gather(int rows = 3, int columns = 2)
    {
      moveX(X_MAX);
      moveY(Y_MAX);
      int SaveMoveX, SaveMoveY;
    
      SaveMoveY = stepperY.currentPosition();
      SaveMoveX = stepperX.currentPosition();
      Iter = rows * columns;
      WorkingProg = 100 / Iter;
    
      for (int j = 1; j < columns + 1; j++) {
        for (int i = 1; i < rows + 1; i++) {
          if (force_stop)
          {
            stopped = true;
            return 1;
          }
          moveY(SaveMoveY);
          moveX(SaveMoveX);
          moveY(stepperY.currentPosition() - (Y_MAX / (rows + 1)));
          SaveMoveY = stepperY.currentPosition();
          SaveMoveX = stepperX.currentPosition();
          if (!force_stop)
          {
            digitalWrite(GRASP_PIN, 0);
            digitalWrite(GRASP_PIN_EN, 1);
            delay(1500);
            digitalWrite(GRASP_PIN_EN, 0);
          }
          else
            return 1;
          moveZ(COLLECT_Z);
          if (!force_stop)
          {
            digitalWrite(GRASP_PIN, 1);
            digitalWrite(GRASP_PIN_EN, 1);
            delay(1500);
            digitalWrite(GRASP_PIN_EN, 0);
          }
          else
            return 1;
          moveZ(COLLECT_Z - 2500);
    
          moveX(250);
          moveY(2000);
          if (!force_stop)
          {
            digitalWrite(GRASP_PIN, 0);
            digitalWrite(GRASP_PIN_EN, 1);
            delay(1500);
            digitalWrite(GRASP_PIN_EN, 0);
            delay(500);
            digitalWrite(GRASP_PIN, 1);
            digitalWrite(GRASP_PIN_EN, 1);
            delay(1500);
            digitalWrite(GRASP_PIN_EN, 0);
          }
          else
            return 1;
          Perc = Perc + WorkingProg;
          if (i == rows)
              moveX(SaveMoveX);
        }
    
        moveX(stepperX.currentPosition() - (X_MAX / (columns + 1)));
        moveY(Y_MAX);
        SaveMoveX = stepperX.currentPosition();
        SaveMoveY = stepperY.currentPosition();
      }
      homeZ();
      return 0;
    }
    
    int SeedGather(int rows = 3, int columns = 2)
     { 
      int SaveMoveX, SaveMoveY,SaveGraspX,SaveGraspY;
      SaveGraspX = 560;
      SaveGraspY = 1000;
      
      moveX(SaveGraspX); //-200 
      moveY(SaveGraspY); //-300
        if (!force_stop)
      {  
        digitalWrite(GRASP_PIN, 0);
        digitalWrite(GRASP_PIN_EN, 1);
        delay(1500);
        digitalWrite(GRASP_PIN_EN, 0);
       }
      else 
        return 1;
      moveZ(COLLECT_Z);
        if (!force_stop)
      {  
        digitalWrite(GRASP_PIN, 1);
        digitalWrite(GRASP_PIN_EN, 1);
        delay(1500);
        digitalWrite(GRASP_PIN_EN, 0);
       }
      else 
        return 1;
    
            
      moveZ(0);
      
      moveX(X_MAX);
      moveY(Y_MAX);
    
      SaveMoveY = stepperY.currentPosition();
      SaveMoveX = stepperX.currentPosition();
      Iter = rows * columns;
      WorkingProg = 100 / Iter;
    
      for (int j = 1; j < columns + 1; j++) {
        for (int i = 1; i < rows + 1; i++) {
          if (force_stop)
          {
            stopped = true;
            return 1;
          }
          
          moveY(SaveMoveY);
          moveX(SaveMoveX);
          moveY(stepperY.currentPosition() - (Y_MAX / (rows + 1)));
          SaveMoveY = stepperY.currentPosition();
          SaveMoveX = stepperX.currentPosition();
          
          moveZ(COLLECT_Z);
          if (!force_stop)
          {
            digitalWrite(GRASP_PIN, 0);
            digitalWrite(GRASP_PIN_EN, 1);
            delay(1500);
            digitalWrite(GRASP_PIN_EN, 0);
          }
          else 
            return 1;
          moveZ(COLLECT_Z - 2500);
          
          Perc = Perc + WorkingProg;
          if (!(i == rows && j == columns))
          {
            SaveGraspY -= 310;
            moveX(SaveGraspX);
            moveY(SaveGraspY);
            moveZ(COLLECT_Z);
    
            if (!force_stop)
              {
            digitalWrite(GRASP_PIN, 1);
            digitalWrite(GRASP_PIN_EN, 1);
            delay(1500);
            digitalWrite(GRASP_PIN_EN, 0);
              }
            else 
            return 1;
            moveZ(0);
          
            if (i == rows)
              moveX(SaveMoveX);
          }
        }
        SaveGraspX -= 280;
        SaveGraspY = 1285;
        moveX(stepperX.currentPosition() - (X_MAX / (columns + 1)));
        moveY(Y_MAX);
        SaveMoveX = stepperX.currentPosition();
        SaveMoveY = stepperY.currentPosition();
      }
      homeZ();
      return 0;
    }
    
    
    int fork(int rows = 3, int columns = 2)
    {
      moveX(X_MAX);
      moveY(Y_MAX);
    
      Iter = rows * columns;
      WorkingProg = 100 / Iter;
    
      for (int j = 1; j < columns + 1; j++) {
        for (int i = 1; i < rows + 1; i++) {
          if (force_stop)
          {
            stopped = true;
            return 1;
          }
          
          moveY(stepperY.currentPosition() - (Y_MAX / (rows + 1)));
          moveZ(FORK_Z);
          delay(5000);
          moveZ(FORK_Z - 2500);
    
          Perc = Perc + WorkingProg;
        }
    
        moveX(stepperX.currentPosition() - (X_MAX / (columns + 1)) + 100);
        moveY(Y_MAX);
      }
      homeZ();
      return 0;
    }
    
    void toggle_light()
    {
      digitalWrite(LIGHT_PIN, !digitalRead(LIGHT_PIN));
      if (digitalRead(LIGHT_PIN) == 1)
        light_enabled = true;
      light_time = millis();
    }
    
    void toggle_pump()
    {
      digitalWrite(PUMP_PIN, !digitalRead(PUMP_PIN));
    }
    
    void lazer_on()
    {
          analogWrite(LAZER, 50);
    }
    
    void lazer_off()
    {
          analogWrite(LAZER, 0);
    }
    
    int use_lazer()
    {
      stepperX.setMaxSpeed(10000);
      stepperY.setMaxSpeed(10000);
      
      stepperX.setSpeed(1600);
      stepperY.setSpeed(1600);
      
      int x0 =X_MAX/2+500;
      int y0 = Y_MAX/2;
      int radius = 350;
      
      int x = 0;
      int y = radius;
      int delta = 1 - 2 * radius;
      int error = 0;
    
      lazer_on();
        
      while(y >= 0) {
        moveXY(x0 + x, y0 + y);
        error = 2 * (delta + y) - 1;
        if(delta < 0 && error <= 0) {
          ++x;
          delta += 2 * x + 1;
          continue;
        }
        error = 2 * (delta - x) - 1;
        if(delta > 0 && error > 0) {
          --y;
          delta += 1 - 2 * y;
          continue;
        }
        ++x;
        delta += 2 * (x - y);
        --y;
      }
      x = radius;
      y = 0;
      delta = 1 - 2 * radius;
      error = 0;
      while(x >= 0) {
        moveXY(x0 + x, y0 - y);
        error = 2 * (delta + y) - 1;
        if(delta < 0 && error <= 0) {
          --x;
          delta += 2 * x + 1;
          continue;
        }
        error = 2 * (delta - x) - 1;
        if(delta > 0 && error > 0) {
          ++y;
          delta += 1 - 2 * y;
          continue;
        }
        --x;
        delta += 2 * (x - y);
        ++y;
      }
    
        x = 0;
        y = radius;
        delta = 1 - 2 * radius;
        error = 0;
        while(y >= 0) {
        moveXY(x0 - x, y0 - y);
        error = 2 * (delta + y) - 1;
        if(delta < 0 && error <= 0) {
          ++x;
          delta += 2 * x + 1;
          continue;
        }
        error = 2 * (delta - x) - 1;
        if(delta > 0 && error > 0) {
          --y;
          delta += 1 - 2 * y;
          continue;
        }
        ++x;
        delta += 2 * (x - y);
        --y;
      }
      
      x = radius;
      y = 0;
      delta = 1 - 2 * radius;
      error = 0;
      while(x >= 0) {
        moveXY(x0 - x, y0 + y);
        error = 2 * (delta + y) - 1;
        if(delta < 0 && error <= 0) {
          --x;
          delta += 2 * x + 1;
          continue;
        }
        error = 2 * (delta - x) - 1;
        if(delta > 0 && error > 0) {
          ++y;
          delta += 1 - 2 * y;
          continue;
        }
        --x;
        delta += 2 * (x - y);
        ++y;
      }
      lazer_off();
      stepperX.setMaxSpeed(1000.0*0.58);
      stepperY.setMaxSpeed(1000.0*0.58);
      stepperX.setSpeed(500*0.58);
      stepperY.setSpeed(500*0.58);
      return 0;
    }
    
    static int z_poz = Z_MAX;
    
    void Z_up()
    {
      if (z_poz < Z_MAX)
      {
        moveZ(z_poz + 100);
        z_poz += 100;
      }
    }
    
    void Z_down()
    {
    
      if (z_poz > 1000)
      {
        moveZ(z_poz - 100);
        z_poz -= 100;
      }
    }
    
    void TestCase1()
    {
      for(int i = 1; i<26; i++)
      {
        moveX(X_MAX);
        moveX(0);
        moveY(Y_MAX);
        moveY(0);
        moveZ(Z_MAX);
        moveZ(0);
      }
    }
    
    void TestCase2()
    {
      for(int i = 1; i<26; i++)
      {
        moveX(X_MAX);
        moveY(Y_MAX);
        moveZ(Z_MAX);
        moveX(0);
        moveY(0);
        moveZ(0);
      }
    }
    
    void TestCase3()
    {
      moveX(200); //450 
      moveY(670); //970
      moveZ(3900);
    }
    
    void TestCase4()
    {
      for(int i = 1; i<3; i++)
      {
        homeALL();
        water();
        homeALL();
        laser();
        homeALL();
        gather();
        homeALL();
        drill();
        homeALL();
        fork();
        homeALL();
        toggle_light();
        SeedGather();
        homeALL();
        toggle_light();
      }
    }
    
::

    //    funkSetup.h

    void funkSetup() {
    
      stepperX.setMaxSpeed(1000.0*0.58);
      stepperX.setSpeed(500*0.58);
      stepperX.setAcceleration(80000);
      stepperX.setPinsInverted(1,0,0);
    
      stepperY.setMaxSpeed(1000.0*0.58);
      stepperY.setSpeed(500*0.58);
      stepperY.setAcceleration(80000);
      stepperY.setPinsInverted(1,0,0);
    
      stepperZ.setMaxSpeed(1000.0);
      stepperZ.setSpeed(500);
      stepperZ.setAcceleration(80000);
      stepperZ.setPinsInverted(0,0,0);
    
      pinMode(ULED1, OUTPUT);
      pinMode(ULED2, OUTPUT);
      pinMode(ULED3, OUTPUT);
      pinMode(ULED4, OUTPUT);
    
      pinMode(LAZER, OUTPUT);
      analogWrite(LAZER, 0);
    
      pinMode(CONTROL_PEREPHERIAL_PIN_SELECT,OUTPUT);
      pinMode(LIGHT_PIN, OUTPUT);
      pinMode(PUMP_PIN, OUTPUT);
       
      digitalWrite(CONTROL_PEREPHERIAL_PIN_SELECT,LOW);
      digitalWrite(LIGHT_PIN,0);
    
      pinMode(ENPin1,OUTPUT);
      pinMode(ENPin2,OUTPUT);
      pinMode(ENPin3,OUTPUT);
      pinMode(ENPin4,OUTPUT);
      
      pinMode(stepPinX,OUTPUT);
      pinMode(stepPinY,OUTPUT);
      pinMode(stepPinZ,OUTPUT);
      pinMode(dirPinX,OUTPUT);
      pinMode(dirPinY,OUTPUT);
      pinMode(dirPinZ,OUTPUT);
      
      pinMode(X_ENDSTOP1,INPUT_PULLUP);
      pinMode(Y_ENDSTOP1,INPUT_PULLUP);
      pinMode(Z_ENDSTOP1,INPUT_PULLUP);
    
      pinMode(GRASP_PIN, OUTPUT);
      pinMode(GRASP_PIN_EN, OUTPUT);
    
    
    }


::

    //    addFunk.h

    void enOFF()
    {
      digitalWrite(ENPin1, 1);
      digitalWrite(ENPin2, 1);
      digitalWrite(ENPin3, 1);
      digitalWrite(ENPin4, 1);
    }
    
    void enON()
    {
      digitalWrite(ENPin1, 0);
      digitalWrite(ENPin2, 0);
      digitalWrite(ENPin3, 0);
      digitalWrite(ENPin4, 0);
    }
    

::

    //    Steppers.h

    int homeZ()
    {
      stepperZ.moveTo(-100000);
      
      while (stepperZ.currentPosition() != stepperZ.targetPosition())
      {
        if (digitalRead(Z_ENDSTOP1) == 1)
        {
          stepperZ.run();
        } else {
          stepperZ.setCurrentPosition(0);
        }
        if (force_stop)
          {
            stopped = true;
            return 1;
          }
      }
      return 0;
    }
    
    int homeX()
    {
      stepperX.moveTo(-100000);
      
      while (stepperX.currentPosition() != stepperX.targetPosition())
      {
        if (digitalRead(X_ENDSTOP1) == 1)
        {
          stepperX.run();
        } else {
          stepperX.setCurrentPosition(0);
        }
        if (force_stop)
          {
            stopped = true;
            return 1;
          }
      }
      return 0;
    }
    
    int homeY()
    {
      stepperY.moveTo(-100000);
      
      while (stepperY.currentPosition() != stepperY.targetPosition())
      {
        if (digitalRead(Y_ENDSTOP1) == 1)
        {
          stepperY.run();
        } else {
          stepperY.setCurrentPosition(0);
        }
        if (force_stop)
          {
            stopped = true;
            return 1;
          }
      }
      return 0;
    }
    
    int homeALL()
    {
      stepperZ.moveTo(-100000);
      stepperX.moveTo(-100000);
      stepperY.moveTo(-100000);
      while ((stepperX.currentPosition() != stepperX.targetPosition()) || (stepperY.currentPosition() != stepperY.targetPosition()) || (stepperZ.currentPosition() != stepperZ.targetPosition()))
      {
        if (digitalRead(Z_ENDSTOP1) == 1)
        {
          stepperZ.run();
        } else {
          stepperZ.setCurrentPosition(0);
    
    
          if (stepperZ.isRunning() == false)
          {
            if (digitalRead(Y_ENDSTOP1) == 1)
            {
              stepperY.run();
            } else {
              stepperY.setCurrentPosition(0);
            }
            if (digitalRead(X_ENDSTOP1) == 1)
            {
              stepperX.run();
            } else {
              stepperX.setCurrentPosition(0);
            }
          }
          if (force_stop)
          {
            stopped = true;
            return 1;
          }
        }
      }
      return 0;
    }
    
    int moveZ(int distance)//движение по OZ, есть проверка расстояния на валидность, отрицательное расстояние - движемся вверх, положительное - вниз
    {
      stepperZ.moveTo(distance);
      if (stepperZ.targetPosition() > Z_MAX)
      {
        stepperZ.moveTo(Z_MAX);
      } else
      {
        if (stepperZ.targetPosition() < 0)
        {
          stepperZ.moveTo(0);
        }
      }
    
      while (stepperZ.currentPosition() != stepperZ.targetPosition())
      {
        if (force_stop)
          {
            stopped = true;
            return 1;
          }
        stepperZ.run();
      }
    }
    
    int moveX(int distance)//движение по OZ, есть проверка расстояния на валидность, отрицательное расстояние - движемся вверх, положительное - вниз
    {
      stepperX.moveTo(distance);
      if (stepperX.targetPosition() > X_MAX)
      {
        stepperX.moveTo(X_MAX);
      } else
      {
        if (stepperX.targetPosition() < 0)
        {
          stepperX.moveTo(0);
        }
      }
    
      while (stepperX.currentPosition() != stepperX.targetPosition())
      {
        if (force_stop)
          {
            stopped = true;
            return 1;
          }
        stepperX.run();
      }
    }
    
    int moveY(int distance)//движение по OZ, есть проверка расстояния на валидность, отрицательное расстояние - движемся вверх, положительное - вниз
    {
      stepperY.moveTo(distance);
      if (stepperY.targetPosition() > Y_MAX)
      {
        stepperY.moveTo(Y_MAX);
      } else
      {
        if (stepperY.targetPosition() < 0)
        {
          stepperY.moveTo(0);
        }
      }
    
      while (stepperY.currentPosition() != stepperY.targetPosition())
      {
        if (force_stop)
          {
            stopped = true;
            return 1;
          }
        stepperY.run();
      }
    }
    
    int moveXY(int x,int y)
    {
      stepperX.moveTo(x);
      stepperY.moveTo(y);
      if (stepperY.targetPosition() > Y_MAX)
      {
        stepperY.moveTo(Y_MAX);
      } else
      {
        if (stepperY.targetPosition() < 0)
        {
          stepperY.moveTo(0);
        }
      }
      if (stepperX.targetPosition() > X_MAX)
      {
        stepperX.moveTo(X_MAX);
      } else
      {
        if (stepperX.targetPosition() < 0)
        {
          stepperX.moveTo(0);
        }
      }
      while (stepperY.currentPosition() != stepperY.targetPosition() || stepperX.currentPosition() != stepperX.targetPosition())
      {
        if (force_stop)
          {
            stopped = true;
            return 1;
          }
        stepperY.runSpeedToPosition();
        stepperX.runSpeedToPosition();
      }
    }
       

::

    //    Setup.h
    
    #define stepPinX 2
    #define dirPinX 5
    AccelStepper stepperX = AccelStepper(1, stepPinX, dirPinX);
    
    #define stepPinY 3
    #define dirPinY 6
    AccelStepper stepperY = AccelStepper(1, stepPinY, dirPinY);
    
    #define stepPinZ 4
    #define dirPinZ 7
    AccelStepper stepperZ = AccelStepper(1, stepPinZ, dirPinZ);
    
    #define X_ENDSTOP1 9
    #define Y_ENDSTOP1 10
    #define Z_ENDSTOP1 11
    
    #define X_MAX 1750
    #define Y_MAX 2500
    #define Z_MAX 5250
    
    #define ENPin1 8
    #define ENPin2 8
    #define ENPin3 8
    #define ENPin4 8
    
    #define ULED1 BDPIN_LED_USER_1
    #define ULED2 BDPIN_LED_USER_2
    #define ULED3 BDPIN_LED_USER_3
    #define ULED4 BDPIN_LED_USER_4
    
    #define analogPin1 A1
    #define analogPin2 A2
    #define analogPin3 A3
    
    
    #define SoilTest 66
    
    #define DHTPIN 67
    #define DHTTYPE DHT11
    DHT dht(DHTPIN, DHTTYPE);
    
    #define LAZER 10
    
    #define DRILL_Z 4450
    #define WATER_Z 3950
    #define FORK_Z 4250
    #define LAZER_Z 3150
    #define COLLECT_Z 4950
    
    #define PUMP_PIN 51
    #define GRASP_PIN 52
    #define GRASP_PIN_EN 59
    #define LIGHT_PIN 57
    #define CONTROL_PEREPHERIAL_PIN_SELECT 58
    
    bool force_stop=false;
    bool stopped = false;
    
    uint64_t light_time=0;
    bool light_enabled=false;
    
    String serial_command;
    
    float WorkingProg;
    float Perc;
    float Iter;
    
        
        
