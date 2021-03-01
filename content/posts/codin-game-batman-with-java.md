---
title: "Codin Game Batman With Java"
sub_title: "Codin Game Batman With Java"
date: 2021-01-26T23:00:55-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/coding-game-small.jpg"
alt_text: "Codin Game Batman With Java screenshot"
year: "2021"
month: "2021/01"
draft: false
categories:
  - Coding challenges
  - Personal
tags:
  - Java
  - Learning
  - challenges
  - Coding
series:
Summary: Batman coding challenge in Java
---
* Challenge: https://www.codingame.com/training/medium/shadows-of-the-knight-episode-1/



``` Java
import java.util.*;
import java.io.*;
import java.math.*;

/**
 * Auto-generated code below aims at helping you parse
 * the standard input according to the problem statement.
 **/
class Player {

    private static class Board{
        public int x0;
        public int y0;
        public int x1;
        public int y1;

        public int bx;
        public int by;

        public Board(int width, int height){
            this.x0 = 0;
            this.y0 = 0;
            this.x1 = width -1;
            this.y1 = height -1;
        }

        public int[] move(String dir){
            this.updateBoard(dir);
            int[] movement = this.getHalfMovement();
            this.setBatman(movement);
            return movement;
        }


        public int[] getHalfMovement(){
            
            return new int[] {
                ((this.x0 + this.x1)/2),
                ((this.y0 + this.y1)/2)
            };
        }
        public Board updateBoard(String dir){
            dir = dir.toUpperCase();
            if(dir.length() > 1){
                if (dir.substring(0, 1).equalsIgnoreCase("U")){
                    this.y1 = by -1;
                } else { // should only be U or D
                    this.y0 = by +1;
                }

                if (dir.substring(1,2).equalsIgnoreCase("R")) {
                    this.x0 = bx +1;
                } else {
                    this.x1 = bx -1;
                }

            } else{
                if (dir.equalsIgnoreCase("U") || dir.equalsIgnoreCase("D")) {
                        this.x0 = bx;
                        this.x1 = bx;
						
                        // Need to update this based on u/d
                        if(dir.equalsIgnoreCase("U")){
                            this.y1 = by -1;
                        }else{
                            this.y0 = by +1;
                        }
                } else {
                        this.y0 = by;
                        this.y1 = by;
						
                        if(dir.equalsIgnoreCase("L")){
                            this.x1 = bx -1;
                        }else{
                            this.x0 = bx +1;
                        }
                }
            }
            return this;
        }
        public Board setBatman(int x, int y){
            this.bx = x;
            this.by = y;
            return this;
        }

        public Board setBatman(int[] xy){
            this.bx = xy[0];
            this.by = xy[1];
            return this;
        }

        public int[] getBatman(){
            return new int[]{bx,by};
        }
    }

    public static void main(String args[]) {
        Scanner in = new Scanner(System.in);
        int W = in.nextInt(); // width of the building.
        int H = in.nextInt(); // height of the building.
        int N = in.nextInt(); // maximum number of turns before game over.
        int X0 = in.nextInt();
        int Y0 = in.nextInt();

        Board board = new Board(W,H).setBatman(X0, Y0);

        /*
            each turn we'll get a relative location of the bomb,
                * Each turn should reduce the amount of searchable space
                    * need an array of possible locations based on where it could be
                    * This will always be in a grid style, so even an x y of heights/widths of 1
                * Each turn should shrink this array, based on what we have so if it should be a square array
                    [x0,y0,x1,y1]
                        originally this will be the entire board set to [0,0,width, height] // remember top left is 0,0
                        // if one of these is missing then we've got at least one good coordinate
                        x0 bottom bound, will only change if right+ shown
                        y0 bottom bound, will only change if +up shown
                        x1 top bound, will only change if down+ shown
                        y1 top bound, will only change if +left shown

                        if up/down, then set x0&x1 to current x position of batman
                        ditto for up

                        if right/left then set y0&y1 to current y position

        */
        
        while (true) {
            
            String bombDir = in.next(); // the direction of the bombs from batman's current location (U, UR, R, DR, D, DL, L or UL)

            int[] movement = board.move(bombDir);
            
            // System.out.printf("%d %d", movement[0],movement[1]); // This doesn't work it just times out
            System.out.println(movement[0] +" " + movement[1]);
        }
    }
}

```