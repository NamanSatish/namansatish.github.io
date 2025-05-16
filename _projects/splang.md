---
layout: distill
title: "Splang: A Playlist Defined Esolang"
description: "Creating an esolang from scratch for CS198-134"
img: assets/img/splang/logo.svg
importance: 13
date: 2025-05-15
category: school
bibliography: empty.bib
toc:
  - name: "Abstract"
  - name: "Language Design"
  - name: "Splang v1.0 Language Reference"
  - name: "Issues"
  - name: "Examples"
  - name: "Conclusion"
authors:
  - name: Naman Satish
    affiliations:
      name: UC Berkeley
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(51, 48, 215, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }
  #inspirational_quote {
    font-style: italic;
    color: #555;
    text-align: center;
    margin: 20px 0;
    /* background-color: #f9f9f9; */
  }
  .splang-logo figure {
    max-width: 250px;      /* adjust to your desired size */
    margin: 0 auto;        /* keep it centered */
  }
  .splang-logo figure img {
    width: 100%;           /* fill the 250px wrapper */
    height: auto;          /* preserve aspect ratio */
  }
---

# Abstract

In this project, I will be creating a programming language called **Splang**. The language is designed to be a fun music-based turning-complete esolang. The language is inspired by the importance a playlist has in a person's life, a way to express oneself, interact with others, and share experiences. With this in mind, I wanted to create a language that is not only functional, but similar to the process of creating a playlist, requires thought and care in the placement of each element. A language that seamlessly enables artistic expression in a functional language.

<div class="container splang-logo">
  {% include figure.liquid 
       loading="lazy" 
       zoomable=true 
       path="assets/img/splang/logo.svg" 
       alt="Splang Logo" 
       imagemagick_disabled=true 
  %}
</div>
<br>
<div class="row mx-auto" style="text-align: center;">
  <div class="col-sm mt-3 mt-md-0" id="inspirational_quote">
  “Music is the universal language of mankind.” - Henry Wadsworth Longfellow
  </div>
</div>

Splang is designed to work with Spotify, a music streaming service and application developed by Spotify Technology S.A. This **feature-rich IDE** enables users to create, edit, share, and collaborate on Splang programs through their playlist interface, all while listening to music! A public method to execute Splang programs is in development, but in the interim, users can run their programs locally through the [Splang interpreter](https://github.com/NamanSatish/splang) written in Python.

# Language Design

A large source of my inspiration for this project came from Piet, a language that uses images as code. In a similar vein, I wanted to create a language that uses music as code, specifically allowing users to create a playlist as a program. While an initial search of music based esolangs showed impressive languages such as PianoFuck by Sam Poder, I only encountered one which operated with Spotify, **Slang**, a esolang using Spotify songs to replace BF's 8 commands. This language demonstrated the potential of using music and playlists as a programming language, but ultimately the language was an extension of BF, and I wanted to create a language that was more expressive, flexible, and took advantage of the choice of songs in a playlist rather than just using genres as commands.

Additionally, I wanted to experience the process of creating a language from scratch, and the challenges that come with it. Instead of using a pre-existing language as a base, and simply implementing a transpiler, I wanted to create a language that was unique and had its own set of commands, syntax, and semantics. This would allow me to explore the design choices that go into creating a language, and the trade-offs that come with those choices.

Because Splang is a language that uses playlists as code, each song in the playlist will represent a command, and the order of the songs determines the flow of the program.

The first question that came to mind was how to represent the commands, there are many attributes of a song that could be used, such as the title, artist, album, genre, and duration. To create a usable language, I needed to choose a set of attributes that would be easy to use, allow for the creation of a enjoyable playlist, and had a good distribution of songs. This ruled out using the title, artist, and album as commands, as there are too many unique songs, and even using factors such as the first letter would have an unequal distribution of songs.

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/splang/first_letter_distribution.png" alt="First Letter Distribution" %}
            <div class="caption">First Letter Distribution</div>
        </div>
    </div>
</div>

As we can see from the chart above, the first letter of a song's title is not evenly distributed, with a large number of songs starting with the letter "S" and barely any in my library with the letter "Q", "X", or "Z". My goal was to ensure the programmer had a good selection of songs to choose from, and didn't necessarily have to spend a long time searching for songs, so the first letter of any alphabetical attribute was not a good choice.

I also ruled out using the genre, as we should be able to create a coherent playlist without having to constantly switch genres. In terms of data visible to the programmer, the only attribute left was the **duration** of the song. This attribute is visible to the programmer formatted as **mm:ss** in the IDE, but is visible in the API as **milliseconds**. As such, we will have to convert the duration in milliseconds to the visible seconds format, rounding our values to the nearest second and converting times such as 1:60 to 2:00. After extensive testing, I was able to reverse engineer the conversion from milliseconds to seconds, as done in the IDE, to have the correct seconds value.

In the same way, we need to ensure that the distribution of these seconds values is even, so I tested my own personal library of songs and 10,000 randomly sampled songs from Spotify. The results were promising, with a good distribution of songs across the seconds values.

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/splang/2d_histogram.png" alt="2D Histogram" %}
            <div class="caption">2D Histogram of seconds values</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/splang/track_duration_distribution.png" alt="Track Duration Distribution" %}
            <div class="caption">Distribution of track lengths</div>
        </div>
    </div>
</div>

As we can see from the charts above, the distribution of seconds values is relatively even, with no statistically significant outliers.

The next step was to determine what kind of language I wanted to create. While a BF extension was a possibility, it had already been done, and even if I could have improved upon it, I had much more than 8 command possibilities. I wanted my language to have built in data structures, such as a stack and heap, and to be able to use the playlist to interact with the data structures. I also wanted to have loops, conditionals, ways to interact with stdin/stdout, labels, jumps, NOPs, and halts. I started my thoughts with the concepts from Whitespace, having certain sequences being treated as IMP sequences, and following songs acting as parameters, and further built upon that with the RISC-V instruction set.

Splang will **not** recieve an update until a time comes that a consensus within the community is reached on the functionality of the remaining 11 commands, however in their current state, they are left to the end user as an opportunity for custom implementations.

As such, I give you **Splang v1.0 Language Reference**.

# Splang v1.0 Language Reference

| **Keyterm** | **Description**                                                                       |
| ----------- | ------------------------------------------------------------------------------------- |
| LS          | Last Second's place, e.g 3:45 --> 5                                                   |
| FS          | First Second's place , e.g 3:45 --> 4                                                 |
| TrackID     | The unique ID of a song in Spotify's database.                                        |
| (song + n)  | The song n positions after the current song in the playlist.                          |
| Stack       | A LIFO data structure, operates on single elements.                                   |
| Heap        | A data structure that stores values at a specific index, operates on single elements. |
| RA Stack    | A LIFO data structure that stores return addresses for function calls.                |

| **Time** | **Command**       | **Parameter(s)** | **Description**                                                                                                                    |
| -------- | ----------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| 0:00     | `NOP`             | None             | No operation, does nothing.                                                                                                        |
| 0:01     | `HALT`            | None             | Halts the program.                                                                                                                 |
| 0:02     | `LABEL`           | 1                | Label, defines the (song + 1)'s trackID as a label.                                                                                |
| 0:03     | `JUMP`            | 1                | Jumps to the label defined by the (song + 1) trackID.                                                                              |
| 0:04     | `JUMPZ`           | 1                | Jumps to the label defined by the (song + 1) trackID if the top of the stack is 0.                                                 |
| 0:05     | `JUMPNZ`          | 1                | Jumps to the label defined by the (song + 1) trackID if the top of the stack is not 0.                                             |
| 0:06     | `JUMPZ_HEAP`      | 1                | Jumps to the label defined by the (song + 1) trackID if the value at the index defined by the (song + 1) trackID in heap is 0.     |
| 0:07     | `JUMPNZ_HEAP`     | 1                | Jumps to the label defined by the (song + 1) trackID if the value at the index defined by the (song + 1) trackID in heap is not 0. |
| 0:08     | `CALL`            | 1                | Pushes return address to RA stack and jumps to the label defined by the (song + 1) trackID.                                        |
| 0:09     | `RETURN`          | None             | Pops the return address from the RA stack and jumps to it.                                                                         |
| 0:10     | `ADD`             | None             | Adds the top two elements of the stack and pushes the result.                                                                      |
| 0:11     | `SUB`             | None             | Subtracts the top two elements of the stack and pushes the result.                                                                 |
| 0:12     | `MUL`             | None             | Multiplies the top two elements of the stack and pushes the result.                                                                |
| 0:13     | `DIV`             | None             | Divides the top two elements of the stack and pushes the result.                                                                   |
| 0:14     | `MOD`             | None             | Modulus of the top two elements of the stack (b mod a) and pushes the result.                                                      |
| 0:15     | `POW`             | None             | Raises the top element of the stack to the power of the second top element and pushes the result.                                  |
| 0:16     | `Not_implemented` | None             | Not implemented.                                                                                                                   |
| 0:17     | `Not_implemented` | None             | Not implemented.                                                                                                                   |
| 0:18     | `Not_implemented` | None             | Not implemented.                                                                                                                   |
| 0:19     | `Not_implemented` | None             | Not implemented.                                                                                                                   |
| 0:20     | `PUSH_LS`         | 1                | Pushes the LS of the (song + 1) duration to the stack.                                                                             |
| 0:21     | `PUSH_FS`         | 1                | Pushes the FS of the (song + 1) duration to the stack.                                                                             |
| 0:22     | `SHIFT_R_LS`      | 1                | Shifts the value of the top element of the stack to the right, adding the LS of the (song + 1)'s duration.                         |
| 0:23     | `SHIFT_L_LS`      | 1                | Shifts the value of the top element of the stack to the left, adding the LS of the (song + 1)'s duration.                          |
| 0:24     | `SHIFT_R_FS`      | 1                | Shifts the value of the top element of the stack to the right, adding the FS of the (song + 1)'s duration.                         |
| 0:25     | `SHIFT_L_FS`      | 1                | Shifts the value of the top element of the stack to the left, adding the FS of the (song + 1)'s duration.                          |
| 0:26     | `POP`             | None             | Pops the top element of the stack.                                                                                                 |
| 0:27     | `DUP`             | None             | Duplicates the top element of the stack.                                                                                           |
| 0:28     | `SWAP`            | None             | Swaps the top two elements of the stack.                                                                                           |
| 0:29     | `Not_implemented` | None             | Not implemented.                                                                                                                   |
| 0:30     | `STORE`           | 1                | Stores the top element of the stack in heap at the index defined by the (song + 1)'s trackID.                                      |
| 0:31     | `STORE_TOP`       | None             | Stores the top element of the stack in the heap at the index defined by the second element of the stack.                           |
| 0:32     | `LOAD`            | 1                | Loads the value at the index defined by the (song + 1)'s trackID from heap to the stack.                                           |
| 0:33     | `LOAD_TOP`        | None             | Loads the value at the index defined by the top element of the stack from heap to the stack.                                       |
| 0:34     | `INC_HEAP`        | 1                | Increments the value at the index defined by the (song + 1)'s trackID in heap.                                                     |
| 0:35     | `DEC_HEAP`        | 1                | Decrements the value at the index defined by the (song + 1)'s trackID in heap.                                                     |
| 0:36     | `INC`             | None             | Increments the top element of the stack.                                                                                           |
| 0:37     | `DEC`             | None             | Decrements the top element of the stack.                                                                                           |
| 0:38     | `Not_implemented` | None             | Not implemented.                                                                                                                   |
| 0:39     | `Not_implemented` | None             | Not implemented.                                                                                                                   |
| 0:40     | `STDIN_INT`       | None             | Reads an integer until a newline from stdin and pushes it to the stack.                                                            |
| 0:41     | `STDIN`           | None             | Reads a string until a newline from stdin and pushes each character to the stack as an ASCII value.                                |
| 0:42     | `STDOUT_INT`      | None             | Pops the top element of the stack and prints it to stdout as an integer.                                                           |
| 0:43     | `STDOUT`          | None             | Pops the top element of the stack and prints it to stdout as an ASCII character.                                                   |
| 0:44     | `READ_CHAR`       | 1                | Reads the first character of the (song + 1)'s title and pushes it to the stack as an ASCII value.                                  |
| 0:45     | `LISTEN`          | 1                | Sleeps for the full duration of the (song + 1).                                                                                    |
| 0:46     | `Not_implemented` | None             | Not implemented.                                                                                                                   |
| 0:47     | `Not_implemented` | None             | Not implemented.                                                                                                                   |
| 0:48     | `Not_implemented` | None             | Not implemented.                                                                                                                   |
| 0:49     | `Not_implemented` | None             | Not implemented.                                                                                                                   |
| 0:50     | `AND`             | None             | Logical AND of the top two elements of the stack and pushes the result.                                                            |
| 0:51     | `OR`              | None             | Logical OR of the top two elements of the stack and pushes the result.                                                             |
| 0:52     | `XOR`             | None             | Logical XOR of the top two elements of the stack and pushes the result.                                                            |
| 0:53     | `NOT`             | None             | Logical NOT of the top element of the stack and pushes the result.                                                                 |
| 0:54     | `EQUAL`           | None             | Checks if the top two elements of the stack are equal and pushes the result.                                                       |
| 0:55     | `NOT_EQUAL`       | None             | Checks if the top two elements of the stack are not equal and pushes the result.                                                   |
| 0:56     | `GREATER`         | None             | Checks if the top element of the stack is greater than the second top element and pushes the result.                               |
| 0:57     | `LESS`            | None             | Checks if the top element of the stack is less than the second top element and pushes the result.                                  |
| 0:58     | `GREATER_EQUAL`   | None             | Checks if the top element of the stack is greater than or equal to the second top element and pushes the result.                   |
| 0:59     | `LESS_EQUAL`      | None             | Checks if the top element of the stack is less than or equal to the second top element and pushes the result.                      |

# Issues

There is inconsistent conversion from duration_ms to displayed duration in the Spotify UI. For example, Sweet Home Alabama by Lynyrd Skynyrd is displayed as 4:43, but the duration_ms is 283800. When playing the song, it may initially flash 4:44 in the playback bar UI, but will then change to 4:43. Different times are displayed on the WebUI vs the CEF application. While timings were completely unpredictable in the WebUI, the CEF application was consistent with most songs, 89 of my randomly sampled songs successfully converted to the correct seconds value, however Sweet Home Alabama never achieved the 4:43 seconds value without ruining the conversion for other songs.

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/splang/sweet_43.png" alt="Sweet Home Alabama 43 seconds" %}
            <div class="caption">4:43 displayed in search results</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/splang/sweet_44.png" alt="Sweet Home Alabama 44 seconds" %}
            <div class="caption">4:44 displayed in playlist view</div>
        </div>
    </div>
</div>

Our takeaway should be : Splang may have recieved its v1.0 release, but it is clear the **IDE is still in ongoing development**.

# Examples

## Hello World

Here is [Hello World in Splang](https://open.spotify.com/playlist/7cPsICy0U7WM8Q67SuYXds?si=0c842d5a0aec44c6), a simple program that prints "HELLOWORLD" to the screen. The program is defined as a playlist, and each song in the playlist represents a command in the language. The program is designed to be edited in the IDE, and run through the Splang interpreter.

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            <iframe style="border-radius:12px" src="https://open.spotify.com/embed/playlist/7cPsICy0U7WM8Q67SuYXds?utm_source=generator" width="100%" height="352" frameBorder="0" allowfullscreen="" allow="autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture" loading="lazy"></iframe>
        </div>
    </div>
</div>

This splang interacts with the stdout, as well as the heap and stack. It takes advantage of loops, jumps, and labels to print **HELLOWORLD**. Shareable through the link `https://open.spotify.com/playlist/7cPsICy0U7WM8Q67SuYXds`, which could be compressed to `7cPsICy0U7WM8Q67SuYXds`, the only part of the URL that is needed to run the program, Splang is clearly a very efficient language and has the incredible property of all programs taking the same amount of local storage, 22 bytes! We will have to thank the IDE developers for providing this service along with our subscription!

Here’s a step-by-step walkthrough of how your playlist program prints **HELLOWORLD**:

1. **PC 0** – `duration_min = 4:44` ⇒ seconds = 44 ⇒ **`READ_CHAR`** (1 param)

   - Param at PC 1: `first_letter = 'D'` ⇒ push `ord('D') = 68` onto the stack
   - Advance PC: 0 + 1 + 1 = **2**

2. **PC 2** – `4:44` ⇒ **`READ_CHAR`**

   - Param at PC 3: `'L'` ⇒ push 76
   - Next PC: **4**

3. **PC 4** – `4:44` ⇒ **`READ_CHAR`**

   - Param at PC 5: `'R'` ⇒ push 82
   - Next PC: **6**

4. **PC 6** – `2:44` ⇒ **`READ_CHAR`**

   - Param at PC 7: `'O'` ⇒ push 79
   - Next PC: **8**

5. **PC 8** – `2:44` ⇒ **`READ_CHAR`**

   - Param at PC 9: `'W'` ⇒ push 87
   - Next PC: **10**

6. **PC 10** – `4:44` ⇒ **`READ_CHAR`**

   - Param at PC 11: `'O'` ⇒ push 79
   - Next PC: **12**

7. **PC 12** – `2:44` ⇒ **`READ_CHAR`**

   - Param at PC 13: `'L'` ⇒ push 76
   - Next PC: **14**

8. **PC 14** – `2:44` ⇒ **`READ_CHAR`**

   - Param at PC 15: `'L'` ⇒ push 76
   - Next PC: **16**

9. **PC 16** – `3:44` ⇒ **`READ_CHAR`**

   - Param at PC 17: `'E'` ⇒ push 69
   - Next PC: **18**

10. **PC 18** – `3:44` ⇒ **`READ_CHAR`**

    - Param at PC 19: `'H'` ⇒ push 72
    - Next PC: **20**

11. **PC 20** – `2:20` ⇒ **`PUSH_LS`** (1 param)

    - Param at PC 21: (last_second = 9) ⇒ push 9
    - Next PC: **22**

12. **PC 22** – `2:36` ⇒ **`INC`**

    - Increment top of stack (9) by 1 ⇒ push 10
    - Next PC: **23**

13. **PC 23** – `4:30` ⇒ **`STORE`** (1 param)

    - Param at PC 24: `track_id = 'Think About'` ⇒ pop 10, store `heap['Think About'] = 10`
    - Next PC: **25**

14. **PC 25** – `4:02` ⇒ **`LABEL`** (1 param)

    - Param at PC 26: `track_id = 'Think About'` ⇒ record `labels['Think About'] = 25 + 1 + 1 = 27`
    - Next PC: **27**

---

### Loop (prints one character per iteration)

Each iteration runs the three instructions below, popping one stack value and decrementing the heap counter until it hits zero.

**a. PC 27** – `3:43` ⇒ **`STDOUT`**

- Pop top of stack and print it as `chr(...)`
- Iterations print, in order:

  1. 72 ⇒ “H”
  2. 69 ⇒ “E”
  3. 76 ⇒ “L”
  4. 76 ⇒ “L”
  5. 79 ⇒ “O”
  6. 87 ⇒ “W”
  7. 79 ⇒ “O”
  8. 82 ⇒ “R”
  9. 76 ⇒ “L”
  10. 68 ⇒ “D”

**b. PC 28** – `3:35` ⇒ **`DEC_HEAP`** (1 param)

- Param at PC 29: `track_id = 'Think About'` ⇒ decrement `heap['Think About']` by 1

**c. PC 30** – `4:07` ⇒ **`JUMPNZ_HEAP`** (1 param)

- Param at PC 31: `track_id = 'Think About'` ⇒ check `heap['Think About']`
- If `heap['Think About'] != 0`, jump back to PC 27; otherwise fall through
- After 10 iterations, `heap['special']` goes from 10 down to 0, so the loop stops.

---

1.  **PC 32** – `3:01` ⇒ **`HALT`**

    - Stop execution

---

**Final printed output:**

```
HELLOWORLD
```

This splang is quite simple, but it is extendable to inputing any given length string, and printing it to the screen. The only limitation is the length of the string, which is limited by the number of songs in the playlist, usually around 10,000 songs for Spotify. However, through your own custom playlist definition, you can bypass this limitation to run local splangs.

## Fibonacci

In development

# Conclusion

Throughout the process of creating Splang, I learned a lot about the challenges and considerations that go into designing a programming language. From choosing the right attributes to represent commands, to designing the syntax and semantics of the language, I gained a deeper understanding of the complexities involved in language design. I also learned about the importance of testing and debugging, and revising my design choices based on feedback and testing results. Overall, this project was a valuable experience that allowed me to explore the world of programming languages and gain practical skills in language design and implementation.
