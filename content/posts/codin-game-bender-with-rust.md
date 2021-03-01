---
title: "Codin Game Bender Ep1 With Rust"
sub_title: "Codin Game Bender Ep1 With Rust"
date: 2021-01-26T22:51:26-06:00
author: "Micheal Arsenault"
author_image: "/images/me_cropped.jpg"
author_alt_text: "Mike!"
image: "/images/coding-game-small.jpg"
alt_text: "Codin Game Bender With Rust screenshot"
year: "2021"
month: "2021/01"
draft: true
categories:
  - Coding challenges
  - Personal
tags:
  - Rust
  - Learning
  - challenges
  - Coding
series:
Summary: My answer to the Bender challenge in Rust
---
* Challenge: https://www.codingame.com/training/medium/bender-episode-1/

For each of these projects I had a lot of fun learning some new languages. I will definitely say THIS IS NOT OPTIMIZED and I am a newbie to Rust, but it was an interesting experience to play around with the borrow checker and learning the bender game.

```Rust
use std::io;
use std::collections::HashMap;

macro_rules! parse_input {
    ($x:expr, $t:ident) => ($x.trim().parse::<$t>().unwrap())
}

#[derive(Copy, Clone, Debug)]
enum Direction{
    NORTH,EAST,WEST,SOUTH,
}

enum Moves{
    LOOP,
    MOVES(Vec<String>),
    None,}

struct Point{
    x:i32,
    y:i32,
}
impl Point{
    fn new()->Point{
        Point{x:-1,y:-1}
    }
    fn add_direction(&self,dir: &Direction)-> Point{
        let mut temp = Point{x:self.x,y:self.y};
        match dir{
            Direction::NORTH => temp.y = self.y -1,
            Direction::EAST => temp.x = self.x +1,
            Direction::WEST => temp.x = self.x -1,
            Direction::SOUTH => temp.y = self.y +1,
        };
        return temp;
    }
}

struct State{
    point: Point,
    inverted: bool,
    beer_mode:bool,
    direction: Direction,
    wall_change: u8,
}
impl State{
    fn get_key(&self)-> String{
        format!("x:{},y:{},i:{},b:{},d:{:?},w:{}",self.point.x,self.point.y,self.inverted,self.beer_mode,self.direction,self.wall_change)
    }
    fn clone(&self) -> State{
        State{
            point: Point{x: self.point.x, y: self.point.y},
            inverted: self.inverted,
            beer_mode: self.beer_mode,
            direction: self.direction,
            wall_change: self.wall_change,
        }
    }
}

struct Board{
    bender_start: Point,
    current_state: State,
    teleports:[Point;2],
    grid: Vec<Vec<char>>,
    moves: Moves,
    moves_taken: HashMap<String,bool> ,
}

impl Board{
    fn get_char(&self, p: &Point)-> char{
        self.grid[p.x as usize][p.y as usize]
    }
    fn update_char_at_point(&mut self, c: char){
        self.grid[self.current_state.point.x as usize][self.current_state.point.y as usize] = c;
    }
    fn can_move_direction(&self) -> bool{
        let c = self.get_char(&self.current_state.point.add_direction(&self.current_state.direction));

        if (c == '#') || (c == 'X' && !self.current_state.beer_mode){
            return false;
        }
        true
    }

    fn get_other_teleport(&self, p: &Point) ->Point{
        if self.teleports[0].x == p.x && self.teleports[0].y == p.y{
            return Point{
                x: self.teleports[1].x,
                y: self.teleports[1].y,
            }
        }
        return Point{
                x: self.teleports[0].x,
                y: self.teleports[0].y,
            }
    }


    fn next_direction(&self) -> Direction{
        match self.current_state.inverted{
            true =>{
                match self.current_state.direction{
                    Direction::NORTH=>Direction::EAST,
                    Direction::EAST=>Direction::SOUTH,
                    Direction::WEST=>Direction::NORTH,
                    Direction::SOUTH=>Direction::WEST,
                }
            },
            false =>{
                match self.current_state.direction{
                    Direction::NORTH=>Direction::WEST,
                    Direction::EAST=>Direction::NORTH,
                    Direction::WEST=>Direction::SOUTH,
                    Direction::SOUTH=>Direction::EAST,
                }
            }
        }
    }

    fn generate_answer(&mut self)->Moves{
        let mut movement_strings: Vec<String> = Vec::new();
        while self.get_char(&self.current_state.point) != '$'{

            match self.moves_taken.insert(self.current_state.get_key(), true){
                // If we're seeing our key again then we've already been here; so it's a loop
                Option::Some(_)=>return Moves::LOOP,
                // If it's a new key then we're good to go on
                Option::None => {

                    match self.get_char(&self.current_state.point){
                        'T' => self.current_state.point = self.get_other_teleport(&self.current_state.point),
                        'I' => self.current_state.inverted = !self.current_state.inverted,
                        'B' => self.current_state.beer_mode = !self.current_state.beer_mode,
                        'N' => self.current_state.direction = Direction::NORTH,
                        'E' => self.current_state.direction = Direction::EAST,
                        'W' => self.current_state.direction = Direction::WEST,
                        'S' => self.current_state.direction = Direction::SOUTH,
                        _ =>{},
                    }

                    if !self.can_move_direction(){
                        self.current_state.direction = if self.current_state.inverted { Direction::WEST } else { Direction::SOUTH };

                        while !self.can_move_direction(){
                            self.current_state.direction = self.next_direction();
                        }
                    }
                    self.current_state.point = self.current_state.point.add_direction(&self.current_state.direction);

                    if self.get_char(&self.current_state.point) == 'X' {
                        // if we're sitting on a wall then we need to remove the wall
                        self.update_char_at_point(' ');
                        self.current_state.wall_change = self.current_state.wall_change +1;
                    }
                    movement_strings.push(format!("{:?}",self.current_state.direction));

                },
            };

        }
        Moves::MOVES(movement_strings)
    }
}

fn main() {
    let mut input_line = String::new();
    io::stdin().read_line(&mut input_line).unwrap();
    let inputs = input_line.split(" ").collect::<Vec<_>>();
    let height = parse_input!(inputs[0], usize);
    let width = parse_input!(inputs[1], usize);

    let mut bender_start: Point = Point::new();
    let mut teleports:[Point;2] = [Point::new(),Point::new()];

    let mut grid = vec![vec![' '; height]; width];


    for i in 0..height as usize {
        let mut input_line = String::new();

        io::stdin().read_line(&mut input_line).unwrap();

        for (idx, c) in  input_line.trim_matches('\n').to_string().chars().enumerate(){
            grid[idx][i] = c;

            match c{
                '@' =>{
                    bender_start = Point{x:idx as i32,y:i as i32};
                },
                'T' =>{
                    match teleports[0].x{
                        -1 =>  teleports[0] = Point{x: idx as i32, y: i as i32},
                        _=>  teleports[1] = Point{x: idx as i32, y: i as i32},
                    }
                },
                _ =>{},
            }
        }

    }


    let current_state = State{
        point: Point{
            x:bender_start.x,
            y:bender_start.y,
        },
        inverted:false,
        beer_mode:false,
        direction:Direction::SOUTH,
        wall_change: 0,
    };

    let mut board  = Board{
        current_state,
        teleports,
        bender_start,
        grid,
        moves: Moves::None,
        moves_taken: HashMap::new()
    };

    match board.generate_answer(){
        Moves::MOVES(m) =>{
            for m in m{
                println!("{}",m);
            }
        },
        Moves::LOOP => println!("LOOP"),
        Moves::None =>{println!("IDK");},

    }

}
```
