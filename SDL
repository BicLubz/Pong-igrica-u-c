//Koristimo SDL datoteku
#pragma once
#define _CRT_SECURE_NO_WARNINGS
#include <SDL.h>	//SDL verzija 2.0
#include <stdlib.h>
#include <stdio.h>
//#include <SDL_image.h>

const int VISINA_P = 640;
const int SIRINA_P = 480;

//#define VISINA_P 640	//visina prozora ovo je bilo prvo ak dode do zajeba
//#define SIRINA_P 480	//sirina prozora

//function prototypes
int init(int w, int h, int argc, char* args[]);

typedef struct lopta_s {

	int x, y; /* pozicija na ekranu*/
	int w, h; // sirina i visina loptice
	int dx, dy; /* vektor kretanja */
} lopta_t;

typedef struct reket {

	int x, y;
	int w, h;
} reket_t;

// Globalne varijable
static lopta_t lopta;
static reket_t reket[2];
int rezultat[] = { 0,0 };
int sirina, visina;		

SDL_Window* prozor = NULL;	//Prozor u kojem cemo renderirati
SDL_Renderer* renderer;		//SDL ce koristiti renderer za crtanje na ekranu

//povrsine
static SDL_Surface* ekran;
static SDL_Surface* naslov;
static SDL_Surface* numbermap;
static SDL_Surface* kraj;

//teksture
SDL_Texture* tekstura_ekrana;

//inicijaliziramo pocetne pozicije i velicine elemenata u igri
static void init_game() {

	lopta.x = ekran->w / 2;
	lopta.y = ekran->h / 2;
	lopta.w = 10;
	lopta.h = 10;
	lopta.dy = 1;
	lopta.dx = 1;

	reket[0].x = 20;
	reket[0].y = ekran->h / 2 - 50;
	reket[0].w = 10;
	reket[0].h = 50;

	reket[1].x = ekran->w - 20 - 10;
	reket[1].y = ekran->h / 2 - 50;
	reket[1].w = 10;
	reket[1].h = 50;
}

int provjeri_rez() {

	int i;

	//loopaj kroz igraceve rezultate
	for (i = 0; i < 2; i++) {

		//provjeri je li rezultat na granici pobjede
		if (rezultat[i] == 10) {

			//resetiraj rezultate
			rezultat[0] = 0;
			rezultat[1] = 0;

			//vrati 1 ako je igracu 1 rezultat na limitu
			if (i == 0) {

				return 1;

				//vrati 2 ako je igracu 2 rezultat na limitu
			}
			else {

				return 2;
			}
		}
	}

	//vrati 0 ako niko nije dosao do 10
	return 0;
}

//ako je return 1 sudar se dogodio, ako je 0, nema sudara
int provjeri_sudar(lopta_t a, reket_t b) {

	int left_a, left_b;
	int right_a, right_b;
	int top_a, top_b;
	int bottom_a, bottom_b;

	left_a = a.x;
	right_a = a.x + a.w;
	top_a = a.y;
	bottom_a = a.y + a.h;

	left_b = b.x;
	right_b = b.x + b.w;
	top_b = b.y;
	bottom_b = b.y + b.h;


	if (left_a > right_b) {
		return 0;
	}

	if (right_a < left_b) {
		return 0;
	}

	if (top_a > bottom_b) {
		return 0;
	}

	if (bottom_a < top_b) {
		return 0;
	}

	return 1;
}

/*ova rutina pomice loptu po svojim vektorima kretanja */
static void move_ball() {

	/* pomici loptu po vektorima kretanja */
	lopta.x += lopta.dx;
	lopta.y += lopta.dy;

	/*ako udari rub ekrana okreni loptu  */
	if (lopta.x < 0) {

		rezultat[1] += 1;
		init_game();
	}

	if (lopta.x > ekran->w - 10) {

		rezultat[0] += 1;
		init_game();
	}

	if (lopta.y < 0 || lopta.y > ekran->h - 10) {

		lopta.dy = -lopta.dy;
	}

	//provjeri za sudar za reketom
	int i;

	for (i = 0; i < 2; i++) {

		int c = provjeri_sudar(lopta, reket[i]);

		//sudar otkriven
		if (c == 1) {

			//lopta se krece lijevo 
			if (lopta.dx < 0) {

				lopta.dx -= 1;

				//lopta se krece desno
			}
			else {

				lopta.dx += 1;
			}

			//promijeni smjer lopte
			lopta.dx = -lopta.dx;

			//promijeni kut lopte ovisno o tome di je udarila reket
			int hit_pos = (reket[i].y + reket[i].h) - lopta.y;

			if (hit_pos >= 0 && hit_pos < 7) {
				lopta.dy = 4;
			}

			if (hit_pos >= 7 && hit_pos < 14) {
				lopta.dy = 3;
			}

			if (hit_pos >= 14 && hit_pos < 21) {
				lopta.dy = 2;
			}

			if (hit_pos >= 21 && hit_pos < 28) {
				lopta.dy = 1;
			}

			if (hit_pos >= 28 && hit_pos < 32) {
				lopta.dy = 0;
			}

			if (hit_pos >= 32 && hit_pos < 39) {
				lopta.dy = -1;
			}

			if (hit_pos >= 39 && hit_pos < 46) {
				lopta.dy = -2;
			}

			if (hit_pos >= 46 && hit_pos < 53) {
				lopta.dy = -3;
			}

			if (hit_pos >= 53 && hit_pos <= 60) {
				lopta.dy = -4;
			}

			//lopta se krece desno
			if (lopta.dx > 0) {

				//teleportiraj loptu kako bi izbjegli multi sudar glitch
				if (lopta.x < 30) {

					lopta.x = 30;
				}

				//lopta se krece lijevo
			}
			else {

				//teleportiraj loptu kako bi izbjegli multi sudar glitch
				if (lopta.x > 600) {

					lopta.x = 600;
				}
			}
		}
	}
}

static void move_paddle_ai() {

	int centar = reket[0].y + 25;
	int centar_ekrana = ekran->h / 2 - 25;
	int brzina_lopte = lopta.dy;

	if (brzina_lopte < 0) {

		brzina_lopte = -brzina_lopte;
	}

	//lopta se krece desno
	if (lopta.dx > 0) {

		// vrati na centar poziciju
		if (centar < centar_ekrana) {

			reket[0].y += brzina_lopte;

		}
		else {

			reket[0].y -= brzina_lopte;
		}

		//lopta se krece lijevo
	}
	else {

		//lopta se krece dole
		if (lopta.dy > 0) {

			if (lopta.y > centar) {

				reket[0].y += brzina_lopte;

			}
			else {

				reket[0].y -= brzina_lopte;
			}
		}

		//lopta se krece gore
		if (lopta.dy < 0) {

			if (lopta.y < centar) {

				reket[0].y -= brzina_lopte;

			}
			else {

				reket[0].y += brzina_lopte;
			}
		}

		//lopta se krece ravno poprijeko
		if (lopta.dy == 0) {

			if (lopta.y < centar) {

				reket[0].y -= 5;

			}
			else {

				reket[0].y += 5;
			}
		}
	}
}

static void move_paddle(int d) {

	//ako je pritisnuta strelica dole pomakni reket dole
	if (d == 0) {

		if (reket[1].y >= ekran->h - reket[1].h) {

			reket[1].y = ekran->h - reket[1].h;

		}
		else {

			reket[1].y += 5;
		}
	}

	//ako je pritisnuta strelica gore pomakni reket gore
	if (d == 1) {

		if (reket[1].y <= 0) {

			reket[1].y = 0;

		}
		else {

			reket[1].y -= 5;
		}
	}
}

static void draw_game_over(int p) {

	SDL_Rect p1;
	SDL_Rect p2;
	SDL_Rect cpu;
	SDL_Rect dest;

	p1.x = 0;
	p1.y = 0;
	p1.w = kraj->w;
	p1.h = 75;

	p2.x = 0;
	p2.y = 75;
	p2.w = kraj->w;
	p2.h = 75;

	cpu.x = 0;
	cpu.y = 150;
	cpu.w = kraj->w;
	cpu.h = 75;

	dest.x = (ekran->w / 2) - (kraj->w / 2);
	dest.y = (ekran->h / 2) - (75 / 2);
	dest.w = kraj->w;
	dest.h = 75;


	switch (p) {

	case 1:
		SDL_BlitSurface(kraj, &p1, ekran, &dest);
		break;
	case 2:
		SDL_BlitSurface(kraj, &p2, ekran, &dest);
		break;
	default:
		SDL_BlitSurface(kraj, &cpu, ekran, &dest);
	}

}

static void draw_menu() {

	SDL_Rect src;
	SDL_Rect dest;

	src.x = 0;
	src.y = 0;
	src.w = naslov->w;
	src.h = naslov->h;

	dest.x = (ekran->w / 2) - (src.w / 2);
	dest.y = (ekran->h / 2) - (src.h / 2);
	dest.w = naslov->w;
	dest.h = naslov->h;

	SDL_BlitSurface(naslov, &src, ekran, &dest);
}

static void draw_background() {

	SDL_Rect src;

	//draw bg with net
	src.x = 0;
	src.y = 0;
	src.w = ekran->w;
	src.h = ekran->h;

	//nacrtaj pozadinu
	//int r = SDL_FillRect(screen,&src,0);

	//if (r !=0){

	//	printf("fill rectangle faliled in func draw_background()");
	//}
}

static void draw_net() {

	SDL_Rect net;

	net.x = ekran->w / 2;
	net.y = 20;
	net.w = 5;
	net.h = 15;

	//draw the net
	int i, r;

	for (i = 0; i < 15; i++) {

		r = SDL_FillRect(ekran, &net, 0xffffffff);

		if (r != 0) {

			printf("fill rectangle faliled in func draw_net()");
		}

		net.y = net.y + 30;
	}
}

static void draw_ball() {

	SDL_Rect src;

	src.x = lopta.x;
	src.y = lopta.y;
	src.w = lopta.w;
	src.h = lopta.h;

	int r = SDL_FillRect(ekran, &src, 0xffffffff);

	if (r != 0) {

		printf("fill rectangle faliled in func drawball()");
	}
}

static void draw_paddle() {

	SDL_Rect src;
	int i;

	for (i = 0; i < 2; i++) {

		src.x = reket[i].x;
		src.y = reket[i].y;
		src.w = reket[i].w;
		src.h = reket[i].h;

		int r = SDL_FillRect(ekran, &src, 0xffffffff);

		if (r != 0) {

			printf("fill rectangle faliled in func draw_paddle()");
		}
	}
}

static void draw_player_0_score() {

	SDL_Rect src;
	SDL_Rect dest;

	src.x = 0;
	src.y = 0;
	src.w = 64;
	src.h = 64;

	dest.x = (ekran->w / 2) - src.w - 12; //12 is just padding spacing
	dest.y = 0;
	dest.w = 64;
	dest.h = 64;

	if (rezultat[0] > 0 && rezultat[0] < 10) {

		src.x += src.w * rezultat[0];
	}

	SDL_BlitSurface(numbermap, &src, ekran, &dest);
}

static void draw_player_1_score() {

	SDL_Rect src;
	SDL_Rect dest;

	src.x = 0;
	src.y = 0;
	src.w = 64;
	src.h = 64;

	dest.x = (ekran->w / 2) + 12;
	dest.y = 0;
	dest.w = 64;
	dest.h = 64;

	if (rezultat[1] > 0 && rezultat[1] < 10) {

		src.x += src.w * rezultat[1];
	}

	SDL_BlitSurface(numbermap, &src, rezultat, &dest);
}

int main(int argc, char* args[]) {

	//SDL Window setup
	if (init(SIRINA_P, VISINA_P, argc, args) == 1) {

		return 0;
	}

	SDL_GetWindowSize(prozor, &sirina, &visina);

	int sleep = 0;
	int quit = 0;
	int state = 0;
	int r = 0;
	Uint32 next_game_tick = SDL_GetTicks();

	//  inicijaliziraj datu pozicije lopte
	init_game();

	//render loop
	while (quit == 0) {

		//provjeri za nove eventove svaki frame
		SDL_PumpEvents();

		const Uint8* keystate = SDL_GetKeyboardState(NULL);

		if (keystate[SDL_SCANCODE_ESCAPE]) {

			quit = 1;
		}

		if (keystate[SDL_SCANCODE_DOWN]) {

			move_paddle(0);
		}

		if (keystate[SDL_SCANCODE_UP]) {

			move_paddle(1);
		}

		//nacrtaj pozadinu
		SDL_RenderClear(renderer);
		SDL_FillRect(ekran, NULL, 0x000000ff);

		//prikaz main meni
		if (state == 0) {

			if (keystate[SDL_SCANCODE_SPACE]) {

				state = 1;
			}

			//nacrtaj meni
			draw_menu();

			//prikaz gameover
		}
		else if (state == 2) {

			if (keystate[SDL_SCANCODE_SPACE]) {
				state = 0;
				//mali delay kako se ne bi space bar dvaput triggero
				//dok se prikazuje main meni
				SDL_Delay(500);
			}

			if (r == 1) {

				//if player 1 is AI if player 1 was human display the return value of r not 3
				draw_game_over(3);

			}
			else {

				//prikaz gameover
				draw_game_over(r);
			}

			//prikaz igre
		}
		else if (state == 1) {

			//provjeri rezultat
			r = provjeri_rez();

			//ako bilo koji igrac pobijedi, promijeni na game over stanje
			if (r == 1) {

				state = 2;

			}
			else if (r == 2) {

				state = 2;
			}

			//reket ai pokreti
			move_paddle_ai();

			// pomakni loptu za sljedeci frame
			move_ball();

			//draw net
			draw_net();

			//nacrtaj rekete
			draw_paddle();

			//* stavi loptu na ekran
			draw_ball();

			//nacrtaj rezultat
			draw_player_0_score();

			//nacrtaj rezultat
			draw_player_1_score();
		}

		SDL_UpdateTexture(tekstura_ekrana, NULL, ekran->pixels, ekran->w * sizeof(Uint32));
		SDL_RenderCopy(renderer, tekstura_ekrana, NULL, NULL);

		//crtaj na display
		SDL_RenderPresent(renderer);

		//vrijeme koje treba da se rendera frame u milisekunde
		next_game_tick += 1000 / 60;
		sleep = next_game_tick - SDL_GetTicks();

		if (sleep >= 0) {

			SDL_Delay(sleep);
		}
	}

	// učitane slike
	SDL_FreeSurface(ekran);
	SDL_FreeSurface(naslov);
	SDL_FreeSurface(numbermap);
	SDL_FreeSurface(kraj);

	//renderanje tekstura koje se koriste
	SDL_DestroyRenderer(renderer);

	//unisti prozor
	SDL_DestroyWindow(prozor);

	//napustiti SDL podsustav
	SDL_Quit();

	return 0;

}

int init(int width, int height, int argc, char* args[]) {

	//inicijaliziranje SDL
	if (SDL_Init(SDL_INIT_VIDEO) < 0) {

		printf("SDL could not initialize! SDL_Error: %s\n", SDL_GetError());

		return 1;
	}

	int i;

	for (i = 0; i < argc; i++) {

		//napravi prozor
		if (strcmp(args[i], "-f")) {

			SDL_CreateWindowAndRenderer(SIRINA_P, VISINA_P, SDL_WINDOW_SHOWN, &prozor, &renderer);

		}
		else {

			SDL_CreateWindowAndRenderer(SIRINA_P, VISINA_P, SDL_WINDOW_FULLSCREEN_DESKTOP, &prozor, &renderer);
		}
	}

	if (prozor == NULL) {

		printf("Window could not be created! SDL_Error: %s\n", SDL_GetError());

		return 1;
	}

	//napravi povrsinu ekrana na kojem ce biti svi elementi nacrtani(lopta, reketi,mreza...)
	ekran = SDL_CreateRGBSurfaceWithFormat(0, 640/*sirina*/, 480/*visina*/, 32, SDL_PIXELFORMAT_RGBA32);

	if (ekran == NULL) {

		printf("Could not create the screen surfce! SDL_Error: %s\n", SDL_GetError());

		return 1;
	}

	//napravi teksturu ekrana da rendera povrsinu ekrana na display
	tekstura_ekrana = SDL_CreateTextureFromSurface(renderer, ekran);

	if (tekstura_ekrana == NULL) {

		printf("Could not create the screen_texture! SDL_Error: %s\n", SDL_GetError());

		return 1;
	}

	//load naslov sliku
	naslov = SDL_LoadBMP("title.bmp");

	if (naslov == NULL) {

		printf("Could not Load title image! SDL_Error: %s\n", SDL_GetError());

		return 1;
	}

	//Load numbermap sliku
	numbermap = SDL_LoadBMP("numbermap.bmp");

	if (numbermap == NULL) {

		printf("Could not Load numbermap image! SDL_Error: %s\n", SDL_GetError());

		return 1;
	}

	//Load gameover sliku
	kraj = SDL_LoadBMP("gameover.bmp");

	if (kraj == NULL) {

		printf("Could not Load title image! SDL_Error: %s\n", SDL_GetError());

		return 1;
	}

	// postavi boju titule
	Uint32 colorkey = SDL_MapRGB(naslov->format, 255, 0, 255);
	SDL_SetColorKey(naslov, SDL_TRUE, colorkey);
	SDL_SetColorKey(numbermap, SDL_TRUE, colorkey);

	return 0;
}
