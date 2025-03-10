/*****************************************************************************
  BM14270AMUV.ino

 Copyright (c) 2020 ROHM Co.,Ltd.

 Permission is hereby granted, free of charge, to any person obtaining a copy
 of this software and associated documentation files (the "Software"), to deal
 in the Software without restriction, including without limitation the rights
 to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 copies of the Software, and to permit persons to whom the Software is
 furnished to do so, subject to the following conditions:

 The above copyright notice and this permission notice shall be included in
 all copies or substantial portions of the Software.

 THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
 THE SOFTWARE.
******************************************************************************/
#include <Arduino.h>
#include <Wire.h>
#include <HardwareSerial.h>
#include <BM14270AMUV.h>

#define SYSTEM_BAUDRATE         (9600)
#define I2C_FREQUENCY           (400000L)
#define VERSION_MAJOR           (1)
#define VERSION_MINOR           (0)
#define DIGIT_NUM               (3)
#define ERROR_WAIT              (1000)    // ms
#define WAIT_TIME               (100)     // ms

bool addr_pin = false;

BM14270AMUV bm14270amuv(addr_pin);
void error_func(int32_t result);

void setup() {
    int32_t result;
    
    Serial.begin(SYSTEM_BAUDRATE);
    while (!Serial) {
    }

  
    Wire.begin();
    (void)Wire.setClock(I2C_FREQUENCY);

    (void)Serial.print("BM14270AMUV sample Code Version ");
    (void)Serial.print(VERSION_MAJOR, DEC);
    (void)Serial.print(".");
    (void)Serial.println(VERSION_MINOR, DEC);

    result = bm14270amuv.init();
    if (result != BM14270AMUV_RESULT_OK) {
        error_func(result);
    }
  return;
}

void loop() {
    int32_t result;
    float current = 0.0;
    
    result = bm14270amuv.get_val(&current);
    if (result == BM14270AMUV_RESULT_OK) {
        (void)Serial.print("BM14270AMUV [Current(A)] = ");
        (void)Serial.println(current, DIGIT_NUM);
    }
    delay(WAIT_TIME);
    
    return;
}

void error_func(int32_t result)
{
    switch (result) {
        case BM14270AMUV_COMM_ERROR :
            (void)Serial.println("BM14270AMUV Communication Error.");
            break;
        
        case BM14270AMUV_PARAM_ERROR :
            (void)Serial.println("BM14270AMUV Parameter Error.");
            break;
        
        default :
            (void)Serial.println("BM14270AMUV Unknown Error.");
            break;
    }
    
    while (1) {
        (void)Serial.println("BM14270AMUV Check System.");
        delay(ERROR_WAIT);
    }
    
    return;
}
