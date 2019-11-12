#include <platform.h>
#include <gpio.h>
#include <stdlib.h>
#include <stdio.h>
#include "delay.h"
#include "lcd.h"
#include "switches.h"
#include "leds.h"
#include "timer.h"
#include "menu.h"
#include "lpc_eeprom.h"


#define sensorIn P0_24 //16

//From EEPROM library
#define PAGE_OFFSET         0x10
#define PAGE_ADDR           0x01

/*! \brief Responsible for recognizing user input in the menu.
 *
 *  Used to set the GPIO as an input, output, and configure the
 *  possible pull-up or pull-down resistors.
 *
 *  \param pin   Pin to set.
 *  \param mode  New output mode of the pin.
 */
void print_switches() {
   if (switch_get(P_SW_UP)) {
		change_state(up);
   }
	 else if (switch_get(P_SW_CR)) {
		 enter_state();
	 }
	 else if (switch_get(P_SW_DN)) {
		change_state(down);
   }
}

void light_leds() {
   leds_set(switch_get(P_SW_UP),
            switch_get(P_SW_CR),
            switch_get(P_SW_DN));
}

/*! \brief The entry method of the program.
 *	
 */
int main(void) {
   switches_init();
   leds_init();
   lcd_init();
   lcd_clear();
	 
	 init_menu();
   while (1) {
		 print_switches();
	 }
}

// *******************************ARM University Program Copyright © ARM Ltd 2014*************************************
