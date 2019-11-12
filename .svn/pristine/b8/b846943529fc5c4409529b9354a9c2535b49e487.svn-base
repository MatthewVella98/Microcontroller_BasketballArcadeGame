#include "menu.h"
#include "delay.h"
#include "game.h"
#include "lpc_eeprom.h"
#include <platform.h>
#include "switches.h"
#include "leds.h"
#include <stdio.h>
#define PAGE_OFFSET         0x10
#define PAGE_ADDR           0x01

/*! \brief Updates the screen based on the state the menu currently is in.
 *
 */
void handle_state();

/*! \brief Loads the score from EEPROM.
 *
 */
void load_score();

/*! \brief Handles the menu for changing the game timer.
 *
 */
void timer_options();


/*! \brief Changes the timer depending on the direction supplied.
 *
 *
 *  \param d   						Direction to change timer(increase or decrease).
 *  \param index  				The current timer choice index of the array of choices.
 *	\param arrayOfTimes		The array of timer options.
 */
void change_timer(Direction d, int *index, int arrayOfTimes[]);

int state;
int menuTimer = 15;

void change_state(Direction d){
	if (d == down && state < 2 ){
		state++;
		handle_state();
	}
	else if (d == up && state > 0){
		state--;
		handle_state();
	}
	delay_ms(250); //not very nice but enough for now
}

void init_menu(){
	state=0;
	handle_state();
}

void handle_state(){
	if (state == 0){
		lcd_clear();
		lcd_set_cursor(0, 0);
		lcd_print("Main menu");
		lcd_set_cursor(0, 1);
		lcd_print("->Play game");
	}
	else if (state == 1){
		lcd_clear();
		lcd_set_cursor(0, 0);
		lcd_print("Play game");
		lcd_set_cursor(0, 1);
		lcd_print("->Options");
	}
	else if (state == 2){
		lcd_clear();
		lcd_set_cursor(0, 0);
		lcd_print("Options");
		lcd_set_cursor(0, 1);
		lcd_print("->Scoreboard");
	}
}

void enter_state(){
	//int timer = 15;
	switch(state){
		case 0:{ //Play game
			start_game(menuTimer); break;
		}
		case 1:{ //Change timer
			timer_options(); break;
		}
		case 2:{ //Scoreboard
			load_score(); break;
		}
	}
}

void timer_options(){
	int timerIndex = 0;
	int timerSettings[4] = {15,30,45,60};
	char convertedNum;
	
	lcd_clear();
	lcd_set_cursor(0,0);
	lcd_print("Time: ");
	lcd_set_cursor(8,0);
	sprintf(&convertedNum, "%d", menuTimer);
	lcd_print(&convertedNum);
	while(1){
		 if (switch_get(P_SW_RT)) {
			change_timer(up, &timerIndex, timerSettings);
		 }
		 else if (switch_get(P_SW_UP)) {
			 break;
		 }
		 else if (switch_get(P_SW_LT)) {
			change_timer(down, &timerIndex, timerSettings);
		 }
 }
  init_menu();
}

void change_timer(Direction d, int *index, int *arrayOfTimes){
	char convertedNum;
		if (d == up && *index < 3){
		*index += 1;
		menuTimer = arrayOfTimes[*index];
	}
	else if (d == down && *index > 0){
		*index -= 1;
		menuTimer = arrayOfTimes[*index];
	}
	lcd_set_cursor(8,0);
	sprintf(&convertedNum, "%d", menuTimer);
	lcd_print(&convertedNum);
	delay_ms(250); //to prevent polling the joystick multiple times between choices
}

void load_score(){
    uint8_t count;
    uint8_t error = 0;
		uint8_t read_buffer[EEPROM_PAGE_SIZE*2];
		uint8_t write_buffer[16]; //16 since screen can handle 16 characters in one line

    EEPROM_Init();

    count = sizeof(write_buffer);
    count &= 0xFC;
		 
    EEPROM_Read(PAGE_OFFSET,PAGE_ADDR,(void*)read_buffer,MODE_16_BIT,count/2);
		
		lcd_clear();
		lcd_set_cursor(0,0);
		lcd_print((char *)read_buffer);
}
