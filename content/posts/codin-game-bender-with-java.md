---
title: "Codin Game Bender With Java"
sub_title: "Codin Game Bender With Java"
date: 2021-01-26T23:05:01-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/coding-game-small.jpg"
alt_text: "Codin Game Bender With Java screenshot"
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
Summary: Bender coding challenge in Java
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

enum Direction{
    NORTH, EAST, WEST, SOUTH,
}

class Point{
    int x,y = 0;

    public Point(int x, int y){
        this.x = x;
        this.y = y;
    }
    @Override 
    public boolean equals(Object obj){
        if (!(obj instanceof Point))
            return false;
        Point other = (Point) obj;
        return this.x == other.x && this.y == other.y;
    }
    public String getKey(){
        return String.format("%d-%d", this.x, this.y);
    }

    public Point addX(int x){
        this.x += x;
        return this;
    }
    public Point addY(int y){
        this.y += y;
        return this;
    }
    public Point clone(){
        return new Point(this.x,this.y);
    }
    public Point addDirection(Direction direction){
        switch (direction){
            case NORTH:
                return this.clone().addY(-1);
            case EAST:
                return this.clone().addX(1);
            case WEST:
                return this.clone().addX(-1);
            case SOUTH:
                return this.clone().addY(1);
            default:
                return this.clone();
        }
    }
}

class State {
    Point point = new Point(0,0);
    boolean  inverted = false;
    boolean beerMode = false;
    Direction direction = Direction.SOUTH;
    int wallChanges = 0;

    public Point getPoint(){
        return this.point;
    }
    public String getKey(){
        return this.point.getKey()+ String.format("-%d-%b-%b-%s", wallChanges,inverted,beerMode,direction.toString());
    }

    @Override
    public boolean equals(Object obj){
        if (!(obj instanceof State))
            return false;

        State other = (State) obj;
        return this.point.equals(other.point) && this.inverted == other.inverted && 
            this.beerMode == other.beerMode && this.direction == other.direction &&
            this.wallChanges == other.wallChanges;
    }

    public State clone(){
        State state = new State();
        state.point = this.point.clone();
        state.inverted = this.inverted;
        state.beerMode = this.beerMode;
        state.direction = this.direction;
        state.wallChanges = this.wallChanges;

        return state;
    }
}
class Board{
    Point[] teleports = null;
    char[][] grid = null;
    State currentState = new State();
    Map<String,List<State>> prevStates = new HashMap<>();

    public Point getOtherTeleport(Point p){
        if (this.teleports[0].equals(p))
            return this.teleports[1];
        return this.teleports[0];
    }
    
    public char getCharAtPoint(Point p){
        return grid[p.x][p.y];
    }
    public void updateCharAtPoint(Point p, char c){
        System.err.println("breaking wall");
        grid[p.x][p.y] = c;
    }
    public boolean alreadyVisited(State state){
        if (prevStates.containsKey(state.getKey())) 
            for (State oldState : prevStates.get(state.getKey())) 
                if (oldState.equals(state))
                    return true;

        return false;
    }
    
    public void move(State state){
        String stateKey = state.getKey();
        if (this.prevStates.containsKey(stateKey)) {
            this.prevStates.get(stateKey).add(state.clone());
        } else {
            List<State> list = new ArrayList<>();
            list.add(state.clone());
            this.prevStates.put(stateKey, list);
        }
    }
    public boolean canMoveDirection(Direction direction, Point currentLocation, boolean isDrunk){
        char location = getCharAtPoint(currentLocation.addDirection(direction));

        if (location == '#' || (location == 'X' && !isDrunk))
            return false;

        return true;
    }

    public Direction nextDirection(Direction direction, boolean isInverted){
// ORIGINAL (SOUTH, EAST, NORTH, WEST)
// Inverted (WEST, NORTH, EAST, SOUTH). 
        switch (direction){
            case NORTH:
                if (isInverted) 
                    return Direction.EAST;
                return Direction.WEST;
            case EAST:
                if (isInverted)
                    return Direction.SOUTH;
                return Direction.NORTH;
            case WEST:
                if (isInverted)
                    return Direction.NORTH;
                return Direction.SOUTH;
            case SOUTH:
                if (isInverted) 
                    return Direction.WEST;
                return Direction.EAST;
            default:
                throw new RuntimeException("DIRECTION PROBLEM");

        }
        // return null;
    }

    public List<String> generateAnswer(){
        List<String> moves = new ArrayList<String>();

        while (getCharAtPoint(this.currentState.getPoint()) != '$') {
            if (alreadyVisited(this.currentState))
                return List.of("LOOP");
            
            // Mark as visited
            move(currentState);
            currentState = currentState.clone();

            switch (getCharAtPoint(currentState.point)){
                // Should't reach this case because we checked for it before we started the loop
                // case '$':
                case 'T':
                    currentState.point = getOtherTeleport(currentState.point);
                    break;
                case 'I':
                    currentState.inverted = !currentState.inverted;
                    break;
                case 'B':
                    currentState.beerMode = !currentState.beerMode;
                    break;
                case 'N':
                    currentState.direction = Direction.NORTH;
                    break;
                case 'E':
                    currentState.direction = Direction.EAST;
                    break;
                case 'W':
                    currentState.direction = Direction.WEST;
                    break;
                case 'S':   
                    currentState.direction = Direction.SOUTH;
                    break;
            }
			if(!canMoveDirection(currentState.direction, currentState.point, currentState.beerMode)){
				currentState.direction = (currentState.inverted) ? Direction.WEST:Direction.SOUTH;
				while(!canMoveDirection(currentState.direction, currentState.point, currentState.beerMode)){
					currentState.direction = nextDirection(currentState.direction, currentState.inverted);
				}
			}
			currentState.point = currentState.point.addDirection(currentState.direction);
			if (getCharAtPoint(currentState.point) == 'X') {
				// If we're sitting on a wall, then we need to remove the wall
				updateCharAtPoint(currentState.point,' ');
				currentState.wallChanges = currentState.wallChanges +1;
			}
			moves.add(currentState.direction.toString());
			System.err.println(String.format("%s::%s",currentState.direction.toString(),currentState.getKey()));
        }
        if (moves.size() == 0)
            moves.add("DON'T KNOW");

        return moves;
    }
}

class Solution {

    public static void main(String args[]) {
        Scanner in = new Scanner(System.in);
        int height = in.nextInt();
        int width = in.nextInt();

        if (in.hasNextLine()) {
            in.nextLine();
        }

        Board board = new Board();
        board.grid = new char[width][height];

        for (int y = 0; y < height; y++) {
            String row = in.nextLine();
            System.err.println(row);
            for (int x = 0; x < row.length(); x++) {
                board.grid[x][y] = row.charAt(x);

                // Handle teleports
                if(board.grid[x][y] == 'T'){
                    if (board.teleports == null){
                        board.teleports = new Point[2];
                        board.teleports[0] = new Point(x,y);
                    } else {
                        board.teleports[1] = new Point(x,y);
                    }
                }
                // Get Bender start:
                if ( board.grid[x][y] == '@')
                    board.currentState.point = new Point(x,y);
                
            }
        }

        for (String move : board.generateAnswer()) 
            System.out.println(move);
            
        
        
    }
}

```