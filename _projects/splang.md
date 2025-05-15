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
---

# Abstract

In this project, I will be creating a programming language called **Splang**. The language is designed to be a fun music-based turning-complete esolang. The language is inspired by the importance a playlist has in a person's life, a way to express oneself, interact with others, and share experiences. With this in mind, I wanted to create a language that is not only functional, but similar to the process of creating a playlist, requires thought and care in the placement of each element. A language that seamlessly enables artistic expression in a functional language.

<div class="row mx-auto" style="text-align: center;">
  <div class="col-sm mt-3 mt-md-0" id="inspirational_quote">
  “Music is the universal language of mankind.” - Henry Wadsworth Longfellow
  </div>
</div>

Splang is designed to work with Spotify, a music streaming service and application developed by Spotify Technology S.A. This feature-rich IDE enables users to create, edit, share, and collaborate on Splang programs through their playlist interface. A public method to execute Splang programs is in development, but in the interim, users can run their programs locally.

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

As we can see from the chart above, the first letter of a song's title is not evenly distributed, with a large number of songs starting with the letter "I" and none in my library with the letter "Y" or "Z". My goal was to ensure the programmer had a good selection of songs to choose from, and didn't necessarily have to spend a long time searching for songs, so the first letter of any alphabetical attribute was not a good choice.

I also ruled out using the genre, as we should be able to create a coherent playlist without having to constantly switch genres. In terms of data visible to the programmer, the only attribute left was the **duration** of the song. This attribute is visible to the programmer formatted as **mm:ss** in the IDE, but is visible in the API as **milliseconds**. As such, we will have to convert the duration in milliseconds to the visible seconds format, rounding our values to the nearest second and converting times such as 1:60 to 2:00. After extensive testing, I was able to reverse engineer the conversion from milliseconds to seconds, as done in the IDE, to have the correct seconds value.

In the same way, we need to ensure that the distribution of these seconds values is even, so I tested my own personal library of songs and 10,000 randomly sampled songs from Spotify. The results were promising, with a good distribution of songs across the seconds values.

<div class="container l-page">
    <div class="row align-items-center text-center">
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/splang/seconds_distribution_personal.png" alt="Seconds Distribution Personal" %}
            <div class="caption">My Personal Library Seconds Distribution</div>
        </div>
        <div class="col">
            {% include figure.liquid loading="lazy" zoomable=true path="assets/img/splang/seconds_distribution_random.png" alt="Seconds Distribution Random" %}
            <div class="caption">Randomly Sampled Library Seconds Distribution</div>
        </div>
    </div>
</div>

As we can see from the charts above, the distribution of seconds values is relatively even, with no statistically significant outliers.

The next step was to determine what kind of language I wanted to create. While a BF extension was a possibility, it had already been done, and even if I could have improved upon it, I had much more than 8 command possibilities. I wanted my language to have built in data structures, such as a stack and heap, and to be able to use the playlist to interact with the data structures. I also wanted to have loops, conditionals, ways to interact with stdin/stdout, labels, jumps, NOPs, and halts.

I wanted to build atop the ideas from Whitespace, having certain sequences being treated as IMP sequences, and following songs acting as parameters
As such, I give you **Splang v1.0 Language Reference**.

# Splang v1.0 Language Reference

LS : Last Second's place, e.g 3:45 --> 5

FS : First Second's place , e.g 3:45 --> 4

TrackID : The unique ID of a song in Spotify's database.

(song + n): The song n positions after the current song in the playlist.

Stack : A LIFO data structure, operates on single elements.

Heap : A data structure that stores values at a specific index, operates on single elements.

RA Stack : A LIFO data structure that stores return addresses for function calls.

| Time | Command           | Parameter(s) | Description                                                                                                                        |
| ---- | ----------------- | ------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| 0:00 | `NOP`             | None         | No operation, does nothing.                                                                                                        |
| 0:01 | `HALT`            | None         | Halts the program.                                                                                                                 |
| 0:02 | `LABEL`           | 1            | Label, defines the next song's trackID as a label.                                                                                 |
| 0:03 | `JUMP`            | 1            | Jumps to the label defined by the (song + 1) trackID.                                                                              |
| 0:04 | `JUMPZ`           | 1            | Jumps to the label defined by the (song + 1) trackID if the top of the stack is 0.                                                 |
| 0:05 | `JUMPNZ`          | 1            | Jumps to the label defined by the (song + 1) trackID if the top of the stack is not 0.                                             |
| 0:06 | `JUMPZ_HEAP`      | 2            | Jumps to the label defined by the (song + 1) trackID if the value at the index defined by the (song + 2) trackID in heap is 0.     |
| 0:07 | `JUMPNZ_HEAP`     | 2            | Jumps to the label defined by the (song + 1) trackID if the value at the index defined by the (song + 2) trackID in heap is not 0. |
| 0:08 | `CALL`            | 1            | Pushes return address to RA stack and jumps to the label defined by the (song + 1) trackID.                                        |
| 0:09 | `RETURN`          | None         | Pops the return address from the RA stack and jumps to it.                                                                         |
| 0:10 | `ADD`             | None         | Adds the top two elements of the stack and pushes the result.                                                                      |
| 0:11 | `SUB`             | None         | Subtracts the top two elements of the stack and pushes the result.                                                                 |
| 0:12 | `MUL`             | None         | Multiplies the top two elements of the stack and pushes the result.                                                                |
| 0:13 | `DIV`             | None         | Divides the top two elements of the stack and pushes the result.                                                                   |
| 0:14 | `MOD`             | None         | Modulus of the top two elements of the stack (b mod a) and pushes the result.                                                      |
| 0:15 | `POW`             | None         | Raises the top element of the stack to the power of the second top element and pushes the result.                                  |
| 0:16 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:17 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:18 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:19 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:20 | `PUSH_LS`         | 1            | Pushes the LS of the next song's duration to the stack.                                                                            |
| 0:21 | `PUSH_FS`         | 1            | Pushes the FS of the next song's duration to the stack.                                                                            |
| 0:22 | `SHIFT_R_LS`      | 1            | Shifts the value of the top element of the stack to the right, adding the LS of the next song's duration.                          |
| 0:23 | `SHIFT_L_LS`      | 1            | Shifts the value of the top element of the stack to the left, adding the LS of the next song's duration.                           |
| 0:24 | `SHIFT_R_FS`      | 1            | Shifts the value of the top element of the stack to the right, adding the FS of the next song's duration.                          |
| 0:25 | `SHIFT_L_FS`      | 1            | Shifts the value of the top element of the stack to the left, adding the FS of the next song's duration.                           |
| 0:26 | `POP`             | None         | Pops the top element of the stack.                                                                                                 |
| 0:27 | `DUP`             | None         | Duplicates the top element of the stack.                                                                                           |
| 0:28 | `SWAP`            | None         | Swaps the top two elements of the stack.                                                                                           |
| 0:29 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:30 | `STORE`           | 1            | Stores the top element of the stack in heap at the index defined by the next song's trackID.                                       |
| 0:31 | `STORE_TOP`       | 0            | Stores the top element of the stack in the heap at the index defined by the second element of the stack.                           |
| 0:32 | `LOAD`            | 1            | Loads the value at the index defined by the next song's trackID from heap to the stack.                                            |
| 0:33 | `LOAD_TOP`        | 0            | Loads the value at the index defined by the top element of the stack from heap to the stack.                                       |
| 0:34 | `INC`             | 1            | Increments the value at the index defined by the next song's trackID in heap.                                                      |
| 0:35 | `DEC`             | 1            | Decrements the value at the index defined by the next song's trackID in heap.                                                      |
| 0:36 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:37 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:38 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:39 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:40 | `STDIN_INT`       | None         | Reads an integer until a newline from stdin and pushes it to the stack.                                                            |
| 0:41 | `STDIN`           | None         | Reads a string until a newline from stdin and pushes each character to the stack as an ASCII value.                                |
| 0:42 | `STDOUT_INT`      | None         | Pops the top element of the stack and prints it to stdout as an integer.                                                           |
| 0:43 | `STDOUT`          | None         | Pops the top element of the stack and prints it to stdout as an ASCII character.                                                   |
| 0:44 | `READ_CHAR`       | 1            | Reads the first character of the next song's title and pushes it to the stack as an ASCII value.                                   |
| 0:45 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:46 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:47 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:48 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:49 | `Not_implemented` | None         | Not implemented.                                                                                                                   |
| 0:50 | `AND`             | None         | Logical AND of the top two elements of the stack and pushes the result.                                                            |
| 0:51 | `OR`              | None         | Logical OR of the top two elements of the stack and pushes the result.                                                             |
| 0:52 | `XOR`             | None         | Logical XOR of the top two elements of the stack and pushes the result.                                                            |
| 0:53 | `NOT`             | None         | Logical NOT of the top element of the stack and pushes the result.                                                                 |
| 0:54 | `EQUAL`           | None         | Checks if the top two elements of the stack are equal and pushes the result.                                                       |
| 0:55 | `NOT_EQUAL`       | None         | Checks if the top two elements of the stack are not equal and pushes the result.                                                   |
| 0:56 | `GREATER`         | None         | Checks if the top element of the stack is greater than the second top element and pushes the result.                               |
| 0:57 | `LESS`            | None         | Checks if the top element of the stack is less than the second top element and pushes the result.                                  |
| 0:58 | `GREATER_EQUAL`   | None         | Checks if the top element of the stack is greater than or equal to the second top element and pushes the result.                   |
| 0:59 | `LESS_EQUAL`      | None         | Checks if the top element of the stack is less than or equal to the second top element and pushes the result.                      |
