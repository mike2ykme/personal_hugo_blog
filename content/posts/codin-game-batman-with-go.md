---
title: "Codin Game Batman With Go"
sub_title: "Codin Game Batman With Go"
date: 2021-01-26T23:03:44-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/coding-game-small.jpg"
alt_text: "Codin Game Batman With Go screenshot"
year: "2021"
month: "2021/01"
draft: true
categories:
  - Coding challenges
  - Personal
tags:
  - Go
  - Learning
  - challenges
  - Coding
series:
Summary: Batman coding challenge in Go
---
* Challenge: https://www.codingame.com/training/medium/shadows-of-the-knight-episode-1/


``` Go

package main

import "fmt"
import "strings"
import "os"

/**
 * Auto-generated code below aims at helping you parse
 * the standard input according to the problem statement.
 **/
type point struct{
    x int
    y int
}

func (p point) toString()string{
    return fmt.Sprintf("%d %d",p.x,p.y)
}
type board struct{
    x0 int
    y0 int
    x1 int
    y1 int

    bx int
    by int
}
func (b *board) move(dir string) point{
    b.updateBoard(dir)
    p := b.getHalfMovement()
    b.setBatman(p)
    return p
}
func (b board) getHalfMovement() point{
    return point{
        ((b.x0 + b.x1)/2),
        ((b.y0 + b.y1)/2),
    }
}

func (b *board) setBatman(p point){
    b.bx = p.x
    b.by = p.y
}
func (b *board) updateBoard(dir string){
    dir = strings.ToUpper(dir)

    if len(dir) > 1 {
        if strings.EqualFold(string(dir[:1]),"U"){
            b.y1 = b.by -1
        } else {
            b.y0 = b.by +1
        }

        if strings.EqualFold(string(dir[1:]),"R"){
            b.x0 = b.bx +1
        } else {
            b.x1 = b.bx -1
        }

    } else {
        if strings.EqualFold(dir,"U") || strings.EqualFold(dir,"D"){
            b.x0 = b.bx
            b.x1 = b.bx
            if strings.EqualFold(dir,"U"){
                b.y1 = b.by -1
            }else {
                b.y0 = b.by +1
            }
        } else { // If L / R 
            b.y0 = b.by
            b.y1 = b.by

            if strings.EqualFold(dir,"L"){
                b.x1 = b.bx -1
            } else {
                b.x0 = b.bx +1
            }
        }
    }

    fmt.Fprintln(os.Stderr,dir)
}

func main() {
    // W: width of the building.
    // H: height of the building.
    var W, H int
    fmt.Scan(&W, &H)
    
    // N: maximum number of turns before game over.
    var N int
    fmt.Scan(&N)
    
    var X0, Y0 int
    fmt.Scan(&X0, &Y0)
    myBoard := board{0,0,W-1,H-1,X0,Y0}
    // _ = myBoard
    for {
        // bombDir: the direction of the bombs from batman's current location (U, UR, R, DR, D, DL, L or UL)
        var bombDir string
        fmt.Scan(&bombDir)
        
        // the location of the next window Batman should jump to.
        m := myBoard.move(bombDir)
        fmt.Println(m.toString())
    }
}

```