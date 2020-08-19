# Python-Projects
#2D Platformer

import pygame
import sys
import random
from pygame.locals import *
pygame.init()

#Making the window appear
win = pygame.display.set_mode((800,480))
pygame.display.set_caption("Just another python game")

#Loading my sprites into pygame
walkRight = [pygame.image.load('E:/UVAN/Coding/Just another python game/R1.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R2.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R3.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R4.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R5.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R6.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R7.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R8.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R9.png')]
walkLeft = [pygame.image.load('E:/UVAN/Coding/Just another python game/L1.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L2.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L3.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L4.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L5.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L6.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L7.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L8.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L9.png')]
bg = pygame.image.load('E:/UVAN/Coding/Just another python game/mountains_4.png')
char = pygame.image.load('E:/UVAN/Coding/Just another python game/standing.png')

clock = pygame.time.Clock()

#All my SFX
bulletSound = pygame.mixer.Sound("C:/Users/Durgha/Pictures/Film Project pics/Python Projects/Pygame-Images/Game/sci-fi_laser.wav")
hitSound = pygame.mixer.Sound("C:/Users/Durgha/Pictures/Film Project pics/Python Projects/Pygame-Images/Game/retro-video-game-sfx-ouch.wav")
jumpSound = pygame.mixer.Sound("C:/Users/Durgha/Pictures/Film Project pics/Python Projects/Pygame-Images/Game/retro-jump.wav")
scoringSound = pygame.mixer.Sound("C:/Users/Durgha/Pictures/Film Project pics/Python Projects/Pygame-Images/Game/retro_scoring_sfx.wav")
gameoverSound = pygame.mixer.Sound("C:/Users/Durgha/Pictures/Film Project pics/Python Projects/Pygame-Images/Game/game-over-arcade.wav")

music = pygame.mixer.music.load("C:/Users/Durgha/Pictures/Film Project pics/Python Projects/Pygame-Images/Game/retro-game-loop_bgm.wav")
pygame.mixer.music.play(-1)
pygame.mixer.music.set_volume(0.2)

white = (255, 255, 255)
green = (0, 200, 0)
red = (200, 0, 0)
black = (0,0,0)

bright_red = (255,0,0)
bright_green = (0,255,0)
sysfont = pygame.font.get_default_font()
score = 0

#Characteristics of the player(I put them into a class for organising)
class player(object):
    def __init__(self, x, y, width, height):
        self.x = x
        self.y = y
        self.width = 64
        self.height = 64
        self.vel = 6
        self.isJump = False
        self.jumpCount = 10
        self.left = False
        self.right = True
        self.walkCount = 0
        self.standing = True
        self.hitbox = (self.x + 17, self.y + 11, 29, 52)

    #Function for drawing the player
    def draw(self,win): 
        if self.walkCount +1 >= 27:
            self.walkCount = 0
        if not (self.standing):
            if self.left:  
                win.blit(walkLeft[self.walkCount//3], (self.x,self.y))
                self.walkCount += 1                          
            elif self.right:
                win.blit(walkRight[self.walkCount//3], (self.x,self.y))
                self.walkCount += 1
        else:
            if self.right:
                win.blit(walkRight[0], (self.x, self.y))
            else:
                win.blit(walkLeft[0], (self.x, self.y))
        self.hitbox = (self.x + 17, self.y + 11, 29, 52)
        pygame.draw.rect(win, (255, 0, 0), self.hitbox, 2)

    #Reducing Points thing
    def hit(self):
        self.isJump = False
        self.jumpCount = 10
        self.x = 5
        self.y = 355
        self.walkCount = 0
        damageFont = pygame.font.SysFont("comicsans", 100)
        damageText = damageFont.render("-5", 1, (255, 0, 0))
        win.blit(damageText, (400 - (damageText.get_width()/2), 240 - (damageText.get_height()/2)))
        pygame.display.update()
        i = 0
        while i < 100:
            pygame.time.delay(10)
            i += 1
            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    i = 101
                    pygame.quit()




 
#Characteristics of the projectiles(This is also in a class)
class projectile(object):
    def __init__(self, x, y, radius, colour, facing,):
        self.x = x
        self.y = y
        self.radius = radius
        self.colour = colour
        self.facing = facing
        self.vel = 20 * facing
    #self.vel defines the speed of the bullet.(The higher the number, the more the speed of the bullet)

    #Function for drawing the projectiles
    def draw(self, win):
        pygame.draw.circle(win, self.colour, (self.x, self.y), self.radius)

#Characteristics of the goblin/enemy
class enemy(object):
    #sprites of the goblin
    walkRight = [pygame.image.load('E:/UVAN/Coding/Just another python game/R1E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R2E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R3E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R4E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R5E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R6E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R7E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R8E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/R9E.png'), pygame.image.load("E:/UVAN/Coding/Just another python game/R10E.png"), pygame.image.load("E:/UVAN/Coding/Just another python game/R11E.png")]
    walkLeft = [pygame.image.load('E:/UVAN/Coding/Just another python game/L1E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L2E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L3E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L4E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L5E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L6E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L7E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L8E.png'), pygame.image.load('E:/UVAN/Coding/Just another python game/L9E.png'), pygame.image.load("E:/UVAN/Coding/Just another python game/L10E.png"), pygame.image.load("E:/UVAN/Coding/Just another python game/L11E.png")]

    def __init__(self, x, y, width, height, end ):
        self.x = x
        self.y = y
        self.width = width
        self.height = height
        self.end = end
        self.path = [self.x, self.end]
        self.walkCount = 0
        self.vel = 3
        self.hitbox = (self.x + 17, self.y + 2, 31, 57)
        self.health = 19
        self.visible = True



    #Defining the walking of the goblin
    def draw(self, win):
        self.move()
        if self.visible:
            if self.walkCount + 1 >= 33:
                self.walkCount = 0
            if self.vel > 0:
                win.blit(self.walkRight[self.walkCount//3], (self.x, self.y))
                self.walkCount += 1
            else:
                win.blit(self.walkLeft[self.walkCount//3], (self.x, self.y))
                self.walkCount += 1
            self.hitbox = (self.x + 17, self.y + 2, 31, 57)
            pygame.draw.rect(win, (255, 0, 0), (self.hitbox[0], self.hitbox[1] - 20, 50, 10))
            pygame.draw.rect(win, (0, 255, 0), (self.hitbox[0], self.hitbox[1] - 20, 53 - ((50/19) * (20 - self.health)), 10))
            pygame.draw.rect(win, (255, 0, 0), self.hitbox, 2)
            

    #Defining the path the goblin walks
    def move(self):
        if self.vel > 0:
            if self.x + self.vel < self.path[1]:
                self.x+=self.vel
            else:
                self.vel = self.vel * -1
                self.walkCount = 0
        else:
            if self.x - self.vel > self.path[0]:
                self.x += self.vel
            else:
                self.vel = self.vel * -1
                self.walkCount = 0
    
    #This is the function that is used when the bullet hits the goblin
    def hit(self):
        if self.health > 0:
            self.health -= 1
        else:
            self.visible = False
        print("CRITICAL HIT")

    
'''
#I copied thid from a website :)
def text_objects(text, font):
    textSurface = font.render(text, True, black)
    return textSurface, textSurface.get_rect()

#I copied this too!
def message_display(text):
    largeText = pygame.font.Font('freesansbold.ttf',85)
    TextSurf, TextRect = text_objects(text, largeText)
    TextRect.center = ((display_width/2),(display_height/2))
    gameDisplay.blit(TextSurf, TextRect)


def quit_game():
    pygame.quit()
    sys.exit()

def button(msg, x, y, w, h, ic, ac,action = None):
    mouse = pygame.mouse.get_pos()
    click = pygame.mouse.get_pressed()
    #print(click)

    #print(mouse)

    if x+ w > mouse[0] > x and y+h > mouse[1] > y:
        pygame.draw.rect(win, ac,(x,y,w,h))
       
    else:
        pygame.draw.rect(win, ic,(x,y,w,h))

    smallText = pygame.font.Font("freesansbold.ttf",20)
    textSurf, textRect = text_objects(msg, smallText)
    textRect.center = ( (x+(w/2)), (y+(h/2)) )
    win.blit(textSurf, textRect)




def game_intro():

    intro = True

    while intro:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit_game()

        win.blit(bg, (0,0))
        largeText = pygame.font.Font(sysfont,55)
        TextSurf, TextRect = text_objects("Just Another Python Game", largeText)
        TextRect.center = ((800/2),100)
        win.blit(TextSurf, TextRect)

        button("Play",350,180,100,50, green, bright_green,"play")
        button("Quit",350,250,100,50, red, bright_red, "quit")

        pygame.display.update()
        clock.tick(15)
        
             '''





#Drawing all the objects in the screen(Use the functions)
def redrawGameWindow():

    win.blit(bg, (0,0))
    bigText = bigFont.render('Score: ' + str(score), 1, (0, 0,0))
    win.blit(bigText, (650, 0))
    man.draw(win)
    goblin.draw(win)
    for bullet in bullets:
        bullet.draw(win)
    pygame.display.update() 



#mainloop
bigFont = pygame.font.SysFont('comicsans', 40)
man = player(5, 355, 64, 64)
goblin = enemy(random.randint(10, 700), 360, 64, 64, 700)
shootLoop = 0
bullets = []
run = True

while run:

    clock.tick(27)

    if goblin.visible == True:
        if man.hitbox[1] < goblin.hitbox[1] + goblin.hitbox[3] and man.hitbox[1] + man.hitbox[3] > goblin.hitbox[1]:
                if man.hitbox[0] + man.hitbox[2] > goblin.hitbox[0] and man.hitbox[0] - man.hitbox[2] < goblin.hitbox[0] + goblin.hitbox[2]:
                    gameoverSound.play()
                    man.hit()
                    score -= 5

    #Limits the no.of bullets you can shoot at a time 
    if shootLoop > 0:
        shootLoop += 1
    if shootLoop > 3:
        shootLoop = 0

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            run = False

    #Movement of the bullets and what happens to the bullet after it hits the goblin's hitbox
    for bullet in bullets:
        if goblin.visible == True:
            if bullet.y - bullet.radius < goblin.hitbox[1] + goblin.hitbox[3] and bullet.y + bullet.radius > goblin.hitbox[1]:
                if bullet.x + bullet.radius > goblin.hitbox[0] and bullet.x - bullet.radius < goblin.hitbox[0] + goblin.hitbox[2]:
                    hitSound.play()
                    hitSound.set_volume(0.3)
                    goblin.hit()
                    score += 1
                    bullets.remove(bullet)

        if bullet.x < 780 and bullet.x > 30:
            bullet.x += bullet.vel
        else:
            bullets.remove(bullet)

    keys = pygame.key.get_pressed()

    if keys[pygame.K_SPACE] and shootLoop == 0:
    #Tracks the player and decides which direction the bullet travels( -1 is left; +1 is right)and bulletsfx
        bulletSound.play()
        bulletSound.set_volume(0.1)
        if man.left:
            facing = -1
        else:
            facing = 1
        #The code below defines the number of bullets which the player can fire.
        if len(bullets) < 10000000000000:
            bullets.append(projectile(round(man.x + man.width //2), round(man.y + man.height //2), 6, (0, 0, 0), facing ))

        shootLoop  = 1
   
    if keys[pygame.K_LEFT] and man.x > man.vel: 
        man.x -= man.vel
        man.left = True
        man.right = False
        man.standing = False

    elif keys[pygame.K_RIGHT] and man.x < 800 - man.vel - man.width:  
        man.x += man.vel
        man.left = False
        man.right = True
        man.standing = False

    else:
        man.standing = True
        man.walkCount = 0
        
    if not(man.isJump):

        if keys[pygame.K_UP]:
            jumpSound.play()
            jumpSound.set_volume(0.2)
            man.isJump = True
         
            man.walkCount = 0
    else:
        if man.jumpCount >= -10:
            man.y -= (man.jumpCount * abs(man.jumpCount)) *0.5
            man.jumpCount -= 1
        else: 
            man.jumpCount = 10
            man.isJump = False
    #game_intro()
    redrawGameWindow()

    
pygame.quit()
