"""
a python version of wordle.
all credits to concepts go to the original creator of wordle, josh wardle.

features:
- color blind mode
- a continuously updating QWERTY keyboard display
- Discord-friendly results sharing feature

known bugs:
- when re-printing board after new attempt, will sometimes not create line breaks at appropriate spots.
    - seems to occur sporadically. no known fix.
"""

import random  # mostly for choosing random solution from data
import time  # to create slight buffers


# opening text files of possible answers and guesses, converting them to lists

# possible solutions
solutionTXT = open("wordle-answers-alphabetical.txt", "r")
solutionData = solutionTXT.read()
solutionList = solutionData.split("\n")
solutionTXT.close()

# possible guesses
guessTXT = open("possiblewords.txt", "r")
guessData = guessTXT.read()
guessList = guessData.split("\n")
guessTXT.close()

# defining functions...


# chooses random word from list as solution
def choose_solution():
    i = random.randint(0, 2314)
    return solutionList[i]


# splits word into list containing individual letters
def split(word):
    return [char for char in word]


# compares guess to solution
def check_word(guess, solution):  # normal mode
    g = split(guess)
    s = split(solution)
    attempt = []  # stores attempt result
    for x in range(len(g)):
        color = "\033[1;37;40m"  # black
        for y in range(len(s)):
            if g[x] == s[y]:
                if x == y:
                    color = "\033[1;30;42m"  # green
                    s[y] = "1"
                    break
                elif x != y:
                    color = "\033[1;30;43m"  # yellow
                    s[y] = "1"
        display = str(color + " " + g[x] + " \033[0;0m")

        attempt.append(display)
    return attempt


# colorblind mode
def check_word_cb(guess, solution):
    g = split(guess)
    s = split(solution)
    attempt = []  # stores attempt result
    for x in range(len(g)):
        color = "\033[1;37;40m"
        for y in range(len(s)):
            if g[x] == s[y]:
                if x == y:
                    color = "\033[1;30;45m"  # purple
                    s[y] = "1"
                    break
                elif x != y:
                    color = "\033[1;30;43m"  # yellow
                    s[y] = "1"
        display = str(color + " " + g[x] + " \033[0;0m")

        attempt.append(display)
    return attempt

# defines keyboard as a list
keyboard = "qwertyuiopasdfghjklzxcvbnm"
key_list = split(keyboard)


# print out keyboard for user to visually keep track of available letters
def keyboard_display(guess, solution, cb):
    eliminate = []  # stores unavailable letters
    keep = []  # stores letters contained in solution

    # using same check_word logic, except result is to add letters to eliminate or keep lists
    if cb == "x":
        g = split(guess)
        s = split(solution)
        for x in range(len(g)):
            color = "\033[1;37;40m"
            for y in range(len(s)):
                if g[x] == s[y]:
                    if x == y:
                        color = "\033[1;30;42m"  # green
                        s[y] = "1"
                        keep.append(g[x])
                        break
                    elif x != y:
                        color = "\033[1;30;43m"  # yellow
                        s[y] = "1"
                        keep.append(g[x])
            if color == "\033[1;37;40m" and g[x] not in keep:
                eliminate.append(g[x])
    elif cb == "c":
        g = split(guess)
        s = split(solution)
        for x in range(len(g)):
            color = "\033[1;37;40m"  # black
            for y in range(len(s)):
                if g[x] == s[y]:
                    if x == y:
                        color = "\033[1;30;45m"  # purple
                        s[y] = "1"
                        keep.append(g[x])
                        break
                    elif x != y:
                        color = "\033[1;30;43m"  # yellow
                        s[y] = "1"
                        keep.append(g[x])
            if color == "\033[1;37;40m" and g[x] not in keep:
                eliminate.append(g[x])

    # to eliminate words, replace them in keyboard with "#"
    for n in range(len(eliminate)):
        if eliminate[n] not in key_list:
            pass
        elif eliminate[n] in key_list and eliminate[n] not in keep:
            i = key_list.index(eliminate[n])
            key_list[i] = "#"

    # print out keyboard
    for i in range(len(key_list)):
        if key_list[i] != "#":  # if letter unavailable, will switch colors
            print("\033[1;30;47m" + key_list[i] + " \033[0;0m", end=" ")
        elif key_list[i] == "#":
            print("\033[1;37;40m" + key_list[i] + " \033[0;0m", end=" ")
        if i == 9:  # line breaks for formatting reasons
            print("\n ", end="")
        if i == 18:
            print("\n   ", end="")


# creates board for sharing (Discord emotes), modifies check_word logic
def modify_check_share(guess, solution):
    g = split(guess)
    s = split(solution)
    share = []
    for x in range(len(g)):
        color = ":black_large_square:"
        for y in range(len(s)):
            if g[x] == s[y]:
                if x == y:
                    color = ":green_square:"  # green
                    s[x] = "1"
                    break
                elif x != y:
                    color = ":yellow_square"  # yellow
                    s[x] = "1"

        share.append(color)
    return share


# colorblind mode
def modify_cb(guess, solution):
    g = split(guess)
    s = split(solution)
    share = []
    for x in range(len(g)):
        color = ":black_large_square:"
        for y in range(len(s)):
            if g[x] == s[y]:
                if x == y:
                    color = ":purple_square:"  # purple
                    s[x] = "1"
                    break
                elif x != y:
                    color = ":yellow_square:"  # yellow
                    s[x] = "1"

        share.append(color)
    return share


# print Discord-friendly board
def share_board(board):
    for i in range(0, len(board)):
        if i % 5 != 4:
            print(board[i], end="")
        elif i % 5 == 4:
            print(board[i], end="\n")


# main mechanism
def play_wordle(cb):
    solution = choose_solution()  # chooses random solution
    guess_num = 0  # will track number of guesses, stops and automatically fails at 6
    board = []  # stores attempts into a single board
    board_share = []  # stores attempts into a single Discord-friendly board
    board_share_cb = []  # colorblind

    # receive guess input from user
    guess = input("Enter a guess (5 letters): ")
    while len(guess) != 5 or type(guess) != str or guess not in guessList:
        guess = input("Enter a guess (5 letters): ")  # loop until valid guess

    # loop guesses until correct or 6 guesses used
    while guess != solution and guess_num < 5:
        guess_num += 1
        print(guess_num)
        if cb == "x":  # normal
            attempt = check_word(guess, solution)
            board = board + attempt  # update board
            for x in board:  # print board
                if board.index(x) % 5 == 4:
                    print(x, end="\n")
                elif board.index(x) % 5 != 4:
                    print(x, end="")
            print()

            attempt_share = modify_check_share(guess, solution)
            board_share = board_share + attempt_share
        elif cb == "c":
            attempt = check_word_cb(guess, solution)
            board = board + attempt
            for x in board:
                if board.index(x) % 5 == 4:
                    print(x, sep="", end="\n")
                elif board.index(x) % 5 != 4:
                    print(x, sep="", end="")
            print()
            attempt_share_cb = modify_cb(guess, solution)
            board_share_cb = board_share_cb + attempt_share_cb

        print()
        keyboard_display(guess, solution, cb)
        print()
        if guess_num == 5:
            print("\nLast guess!")

        guess = input("Enter a guess (5 letters): ")
        while len(guess) != 5 or type(guess) != str or guess not in guessList:
            guess = input("Enter a guess (5 letters): ")

    guess_num += 1
    print(guess_num)

    if cb == "x":
        attempt = check_word(guess, solution)
        for x in attempt:
            print(x, sep="", end="")
        print()

        attempt_share = modify_check_share(guess, solution)
        board_share = board_share + attempt_share
    elif cb == "c":
        attempt = check_word_cb(guess, solution)
        for x in attempt:
            print(x, sep="", end="")
        print()
        attempt_share_cb = modify_cb(guess, solution)
        board_share_cb = board_share_cb + attempt_share_cb

    if guess == solution:
        if cb == "x":
            attempt = check_word(guess, solution)
        if cb == "c":
            attempt = check_word_cb(guess, solution)
        print("Great work. The word was:", solution)
    elif guess_num >= 5:
        if cb == "x":
            attempt = check_word(guess, solution)
        if cb == "c":
            attempt = check_word_cb(guess, solution)
        print("Nice try! The word was:", solution)
        guess_num = "X"

    print("Share your results!")
    print("WordleCopy ", guess_num, "/6", sep="")
    if cb == "x":
        share_board(board_share)
    elif cb == "c":
        share_board(board_share_cb)


def main():
    # instructions
    print("This is a copy of Wordle.")
    print("Instructions:")
    print("You have six attempts to guess the correct five-letter word.")
    print("If a letter in your guess is in the correct position, it will turn green (or purple).")
    print("If a letter is in the correct word, but not in the correct position, it will turn yellow.")
    print("If a letter is not in the correct word, it will not be highlighted.")

    # literally unnecessary short loading sequence because it looks cooler
    time.sleep(0.3)
    print("")
    print("Loading.", end="")
    time.sleep(0.3)
    print(".", end="")
    time.sleep(0.3)
    print(".")
    time.sleep(0.3)

    # user chooses colorblind mode
    cb = input("Type x for normal, c for colorblind; then press Enter.\n> ")
    while cb != "x" and cb != "c":
        cb = input("Type x for normal, c for colorblind; then press Enter.\n> ")

    play_wordle(cb)

    print("You are now exiting Wordle.")


main()
