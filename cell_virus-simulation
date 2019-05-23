#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Created on Fri Apr 12 00:59:52 2019

@author: jessicapeng
"""

import random
import math
from matplotlib import pyplot as plt
import numpy as np

def image_example():
    '''should produce red,purple,green squares
    on the diagonal, over a black background'''
    # RGB indexes
    red,green,blue = range(3)
    gray = (0.5, 0.5, 0.5)

    # img array 
    # all zeros = black pixels
    # shape: (150 rows, 150 cols, 3 colors)
    img = np.zeros((150,150,3))
    for x in range(50):
        for y in range(50):
            # red pixels
            img[x,y,red] = 1.0
            # purple pixels
            # set 3 color components 
            img[x+50, y+50,:] = (.5,0,.5)
            # green pixels
            img[x+100,y+100, green] = 1.0
    plt.imshow(img)
    
image_example()

def normpdf(x, mean, sd):
    """
    Return the value of the normal distribution 
    with the specified mean and standard deviation (sd) at
    position x.
    You do not have to understand how this function works exactly. 
    """
    var = float(sd)**2
    denom = (2*math.pi*var)**.5
    num = math.exp(-(float(x)-float(mean))**2/(2*var))
    return num/denom

def pdeath(x, mean, sd):
    start = x-0.5
    end = x+0.5
    step =0.01    
    integral = 0.0
    while start<=end:
        integral += step * (normpdf(start,mean,sd) + normpdf(start+step,mean,sd)) / 2
        start += step            
    return integral    
    
recovery_time = 4 # recovery time in time-steps
virality = 0.6   # probability that a neighbor cell is infected in 
                  # each time step                                                  

#contains x and y coordinate of each instance
#contains current state of cell 
class Cell(object):

    def __init__(self,x, y): #constructor for cell
        self.x = x
        self.y = y 
        self.state = "S" # can be "S" (susceptible), "R" (resistant = dead), or 
                         # "I" (infected)
        self.time_infected = 0 
        
    def infect(self):
        self.state = "I"
        self.time_infected = 0 #calling infect sets time back to 0
        
    def process(self, adjacent_cells): #processes each cell for time steps
        #The process method is only relevant if the status of the cell is "I"
        #and has been infected for at least one
        #time step. 
        
        #Otherwise, the method can just increment the time data
        #field and then return immediately.  
        
        if (self.state != "I" and self.time_infected == 0):
            self.time_infected += 1 
            return
        
        if(self.state == "I"):
            random_death = random.random()
            
            self.time_infected += 1
            
             #probability of recovery (done before death)
            if(self.time_infected == recovery_time):
               self.state = "S" 
               self.time_infected = 0
               
            #probability of death
            elif random_death < pdeath(self.time_infected, 3, 1): #3 is day of highest prob deat rate, 1 is std dev
            #cell dies
               self.state = "R"
               
               
               #if it dies/recovers it can't infect others right? yes      
            else:
                for cell in adjacent_cells:
                    if cell.state == "S": #the cell can become infected
                #determine probability of cell being infected with virality 
                        prob_infected = random.random() #generate float between 0.0. and 1.0
                #If that number is <= the virality, 
                #call the infect() method of the neighboring cell. 
                #!: call neighboring cell or the cell itself?
                        if prob_infected < virality:
                            cell.infect()

#main class that implements simulator
#contains attributes for height and width'
#contians dictionary cells that store the actual cells on the map
#keys on dictionary: tuples of (x,y) coordinates, values: cell instances
class Map(object):
    
    def __init__(self):
        self.height = 150
        self.width = 150           
        self.cells = {}

#This method takes a cell object as 
#its parameter and inserts it into the 
#cells dictionary.
    def add_cell(self, cell):
        #create a new instance of cell
        #create a tuple (aka key) of cell by using x & y
        key = (cell.x, cell.y)
        #add cell into dictionary with tupe of location as key
        self.cells[key] = cell
     
    
    #to plot map, turn data in map into suitable format, then call matplotlib
    def display(self):
        #initiate colors (?)
        #red,green,blue = range(3)
        #gray = (0.5, 0.5, 0.5)
    

        #creates a 150 x 150 map
        #i think it makes everything black ish
        #of 3 colors?
        img = np.zeros((150,150,3))
        
        #iterate through dictionary containing cooridnates & cells
        for coordinates in self.cells.keys():
            #set x & y to the coordinates
            x, y = coordinates[0], coordinates [1]
            #check what state S, R, or I to determine color
            if self.cells[coordinates].state == "S":
                #color = green
                img[x,y,:] = (0.0,1.0,0.0)
            elif self.cells[coordinates].state == "I":
                #color = red
                img[x,y,:] = (1.0,0.0,0.0)
            elif self.cells[coordinates].state == "R":
                #color = gray
                img[x,y,:] = (0.5,0.5,0.5)
            #set the cell at coordinates to its color 
            #img[x, y, color] = 1.0 #uh is this correct? (doesn't work for gray)
            
        #plot the map
        plt.imshow(img) #to display the map
                          #requires image to be a numpy array

        #RGB green: (0.0, 1.0, 0.0)
        #RGB red: (1.0, 0.0, 0.0)
        #RGB gray: (0.5, 0.5, 0.5)
        
    def check_bounds(self, a, b):
         x = a
         y = b
         return (x >= 0 and x < 150 and y >= 0 and y < 150)
    
    #that returns a list of cell instances that are adjacent to
    #coordinate (x,y). Adjacent means that the cell could be to 
    #the north, east, south, or west. Diagonal cells do not 
    #count as adjacent. Pay attention to the boundary of the 
    #map. There cannot be any cells outside of the map area.      
    def adjacent_cells(self, x, y):
        #list to store all the adjacent coordinates
        #print('Cell is: x:', x, 'y:', y)
        
        adjacent_coord = []
        
        #list to store all adjacent cells
        adjacent_cells_list = []

        #I think that indexes of the map should
        #go from 0 to 149 right?
        
        #check if after you + 1 or - 1 N, S, E, W that it 
        #isn't less than 0 or past 149
        north = (x, y - 1)
        south = (x, y + 1)
        east = (x + 1, y)
        west = (x - 1, y)
        
        #debuggin
        #print("N:", north)
        #print("S:", south)
        #print("E:", east)
        #print("W:", west)
        
        #append all tuple of coordinates to list to iterate
        adjacent_coord.append(north)
        adjacent_coord.append(south)
        adjacent_coord.append(east)
        adjacent_coord.append(west)
        

        for coord_tuple in adjacent_coord:
            #if within bounds and exists as a cell in map
            if(self.check_bounds(coord_tuple[0],coord_tuple[1]) and coord_tuple in self.cells.keys()):
                #print("HELLO THE TUPLE IS", coord_tuple)
                #search in dictionary and put cell into temp cell var
                adj_cel = self.cells[coord_tuple]
                #append to list
                adjacent_cells_list.append(adj_cel)
        
        #return list
        return adjacent_cells_list

    def time_step(self):
        
        for coord in self.cells.keys():
            #print("Cell at", coord)
            adj_cells_list = self.adjacent_cells(coord[0], coord[1])
            self.cells[coord].process(adj_cells_list)
        
        self.display()
            
        
#This function should reads in x,y coordinates from a file, create a 
#new Cell instance for each coordinate pair. The function should return a Map instance 
#containing all the cells. 
#The file nyc_map.csv contains the coordinates for each cell of the
#150x150 New York City map in comma separated value format.                  
def read_map(filename):
    
    m = Map()
   #open file for reading
    f = open('nyc_map.csv', 'r') #do we need to ask for other filename in display
        #for each line in f
    for line in f:
            #strip the \n spaces
        line.strip()
            #split by comma to get x & y coordinates
        fields = line.split(',')
            #create a new cell & set values to the ones from file
        new_cell = Cell(int(fields[0]), int(fields[1]))
            #create tuple key
        #key = (new_cell.x, new_cell.y)
            #add the cell to maps
        m.add_cell(new_cell)
    
    return m
