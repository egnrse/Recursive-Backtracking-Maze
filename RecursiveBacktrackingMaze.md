#!/usr/bin/env python
import tkinter as tk
import random, time, string
from PIL import Image, ImageDraw
class Application(tk.Frame):
	#in Cells
	cWidth = 170
	cHeight = 100
	widthCells = 10
	
	Lines = []
	Cells = {}
	PositionHistory = []
	Backtracked = False
	Return = False
	x0 = []
	y0 = []
	x1 = []
	y1 = []
	now = time.strftime('%S')
	x = 0
	
	def __init__(self, master=None):
		tk.Frame.__init__(self, master)
		self.grid()
		self.createInterface()
		
	def createInterface(self):
		self.backtrackingButton = tk.Button(self, text='create & save a Recursive Backtracking Algorithm', command=self.createBacktrackingMaze)
		self.cWidthEntry = tk.Entry(self)
		self.cHeightEntry = tk.Entry(self)
		self.widthCellsEntry = tk.Entry(self)
		self.descriptionLabel1 = tk.Label(self, text='cWidth')
		self.descriptionLabel2 = tk.Label(self, text='cHeight')
		self.descriptionLabel3 = tk.Label(self, text='widthCells')
		
		self.backtrackingButton.grid(columnspan=2, sticky=tk.W+tk.E)
		self.cWidthEntry.grid(column=1, row=1, sticky=tk.W+tk.E)
		self.cHeightEntry.grid(column=1, row=2, sticky=tk.W+tk.E)
		self.widthCellsEntry.grid(column=1, row=3, sticky=tk.W+tk.E)
		self.descriptionLabel1.grid(row=1, sticky=tk.W+tk.N+tk.E+tk.S)
		self.descriptionLabel2.grid(row=2, sticky=tk.W+tk.N+tk.E+tk.S)
		self.descriptionLabel3.grid(row=3, sticky=tk.W+tk.N+tk.E+tk.S)
	
	def reset(self):
		self.Progressing()
		try:
			self.cWidth = int(self.cWidthEntry.get())
			self.cHeight = int(self.cHeightEntry.get())
			self.widthCells = int(self.widthCellsEntry.get())
		except:
			pass
		if self.cWidth*self.cHeight*self.widthCells > 1000000:
			print("			!! Alert !!")
			print(" very big Numbers can cause longer progressingtime")
			print(str(self.cWidth*self.cHeight*self.widthCells)+' Pixel to edit')
		if self.widthCells < 10:
			print("if 'widthCells' is smaller than 10, the Maze isn't solvable")
		self.Progressing()
		self.now = time.strftime('%S')
		self.x = 0
		
		self.Lines = []
		self.x0 = []
		self.y0 = []
		self.x1 = []
		self.y1 = []

		self.Cells.clear()
		self.PositionHistory = []
		self.Backtracked = False
		self.Return = False
		
	def id_generator(self, size=6, chars=string.ascii_uppercase + string.digits):
		return ''.join(random.choice(chars) for _ in range(size))
	
	def InitCells(self):
	#'row,column':status	#0:not visited; 1:visited
		for I in range(0, self.cHeight):
			for i in range(0, self.cWidth):
				Position = str(I) +','+ str(i)
				self.Cells[Position] = 0
				self.Progressing()
				
	def StartEnd(self):
		fullRow = self.Lines[0]
		fullRow[random.randrange(1, self.cWidth*2, 2)] = ''
		
		fullRow = self.Lines[2*self.cHeight]
		fullRow[random.randrange(1, self.cWidth*2, 2)] = ''
		startPosition = '0,0'
		return startPosition
	
	def createGrid(self):
		for I in range(0, (self.cHeight)*2+1, 2):
			self.Progressing()
			self.Lines.append([])
			self.Lines.append([])
			self.x0.append([])
			self.x0.append([])
			self.y0.append([])
			self.y0.append([])
			self.x1.append([])
			self.x1.append([])
			self.y1.append([])
			self.y1.append([])
			for i in range(0, (self.cWidth)*2+1):
				self.Progressing()
				self.x0[I].append(i*self.widthCells +self.widthCells/2)
				self.y0[I].append(I*self.widthCells)
				self.x1[I].append(i*self.widthCells +self.widthCells/2)
				self.y1[I].append(I*self.widthCells + self.widthCells)
				self.Lines[I].append('exists')
				self.Progressing()
		for I in range(1, (self.cHeight)*2, 2):
			for i in range(0, (self.cWidth)*2+1, 2):
				self.Progressing()
				self.x0[I].append(i*self.widthCells +self.widthCells/2)
				self.y0[I].append(I*self.widthCells)
				self.x1[I].append(i*self.widthCells +self.widthCells/2)
				self.y1[I].append(I*self.widthCells + self.widthCells)
				self.Lines[I].append('exists')
				self.Progressing()
		self.InitCells()
		
	def findNextPosition(self, currentPosition):
		#searching for the next Position
		row,column = currentPosition.split(",")
		row = int(row)
		column = int(column)
		upPosition = str(row-1)+','+str(column)
		downPosition = str(row+1)+','+str(column)
		leftPosition = str(row)+','+str(column-1)
		rightPosition = str(row)+','+str(column+1)
		order = random.randrange(0, 4)
		self.Progressing()
		if order == 0:
			if upPosition in self.Cells and self.Cells[upPosition] == 0:
				nextPosition = upPosition
				return nextPosition
			elif leftPosition in self.Cells and self.Cells[leftPosition] == 0:
				nextPosition = leftPosition
				return nextPosition
			elif downPosition in self.Cells and self.Cells[downPosition] == 0:
				nextPosition = downPosition
				return nextPosition
			elif rightPosition in self.Cells and self.Cells[rightPosition] == 0:
				nextPosition = rightPosition
				return nextPosition
			else:
				return self.Backtracking(currentPosition)
		self.Progressing()
		if order == 1:
			if downPosition in self.Cells and self.Cells[downPosition] == 0:
				nextPosition = downPosition
				return nextPosition
			elif rightPosition in self.Cells and self.Cells[rightPosition] == 0:
				nextPosition = rightPosition
				return nextPosition
			elif leftPosition in self.Cells and self.Cells[leftPosition] == 0:
				nextPosition = leftPosition
				return nextPosition
			elif upPosition in self.Cells and self.Cells[upPosition] == 0:
				nextPosition = upPosition
				return nextPosition
			else:
				return self.Backtracking(currentPosition)
		self.Progressing()
		if order == 2:
			if rightPosition in self.Cells and self.Cells[rightPosition] == 0:
				nextPosition = rightPosition
				return nextPosition
			elif downPosition in self.Cells and self.Cells[downPosition] == 0:
				nextPosition = downPosition
				return nextPosition
			elif upPosition in self.Cells and self.Cells[upPosition] == 0:
				nextPosition = upPosition
				return nextPosition
			elif leftPosition in self.Cells and self.Cells[leftPosition] == 0:
				nextPosition = leftPosition
				return nextPosition
			else:
				return self.Backtracking(currentPosition)
		self.Progressing()
		if order == 3:
			if leftPosition in self.Cells and self.Cells[leftPosition] == 0:
				nextPosition = leftPosition
				return nextPosition
			elif upPosition in self.Cells and self.Cells[upPosition] == 0:
				nextPosition = upPosition
				return nextPosition
			elif rightPosition in self.Cells and self.Cells[rightPosition] == 0:
				nextPosition = rightPosition
				return nextPosition
			elif downPosition in self.Cells and self.Cells[downPosition] == 0:
				nextPosition = downPosition
				return nextPosition
			else:
				return self.Backtracking(currentPosition)
				
	def Backtracking(self, currentPosition):
		self.Progressing()
		try:
			lastPosition = self.PositionHistory.pop()
			self.Backtracked = True
			return lastPosition
		except:
			self.Return = True
		
	def delWall(self, currentPosition, nextPosition):
		self.Progressing()
		try:
			rowCP,columnCP = currentPosition.split(",")
			rowNP,columnNP = nextPosition.split(",")
			rowCP = int(rowCP)
			columnCP = int(columnCP)
			rowNP = int(rowNP)
			columnNP = int(columnNP)
			if rowCP < rowNP:			#down
				fullRow = self.Lines[2*rowCP+2]
				fullRow[columnCP*2+1] = ''
			elif rowCP > rowNP:			#up
				fullRow = self.Lines[2*rowCP]
				fullRow[columnNP*2+1] = ''
			elif columnCP > columnNP:	#left
				fullRow = self.Lines[2*rowCP+1]
				fullRow[columnNP+1] = ''
			elif columnCP < columnNP:	#right
				fullRow = self.Lines[2*rowCP+1]
				fullRow[columnNP] = ''
		except:
			self.Return = True
			
	def Progressing(self):
		Anzahl = ''
		if not self.now == time.strftime('%S'):
			self.x += 1
			self.now = time.strftime('%S')
			if self.x > 4:
				self.x = 1
			for i in range(0, self.x):
				Anzahl += '.'
			print("progressing"+Anzahl)
	
	def Print(self):
		width = self.cWidth*self.widthCells*2 + self.widthCells
		height = self.cHeight*self.widthCells*2 + self.widthCells
		picture = Image.new('L', (width, height), color=255)
		drawingBoard = ImageDraw.Draw(picture)
		for I in range(0,len(self.Lines)):
			fullRow = self.Lines[I]
			fullx0 = self.x0[I]
			fully0 = self.y0[I]
			fullx1 = self.x1[I]
			fully1 = self.y1[I]
			for i in range(0, len(fullRow)):
				self.Progressing()
				if fullRow[i] == 'exists':
					drawingBoard.line([(fullx0[i], fully0[i]), (fullx1[i], fully1[i])], width=self.widthCells)
		Path = "BMaze"+self.id_generator()+".png"
		if self.cWidth*self.cHeight*self.widthCells > 1000000:
			print("saveing very big Pictures may take a while")
		picture.save(Path, format='PNG')
		try:
			picture.show()
		except:
			print("Pictures that are that big can't be shown")
			print("Please open it in the Explorer")
		return picture
	
	def createBacktrackingMaze(self):
		print("creating Backtrackingmaze")
		self.reset()
		self.createGrid()
		currentPosition = self.StartEnd()
		while not self.Return:
			self.Progressing()
			nextPosition = self.findNextPosition(currentPosition)
			self.delWall(currentPosition, nextPosition)
			
			self.Progressing()
			self.Cells[currentPosition] = 1
			self.PositionHistory.append(currentPosition)
			if self.Backtracked:
				del self.PositionHistory[-1]
				self.Backtracked = False
			currentPosition = nextPosition
		picture = self.Print()
		print("finished")
		
app = Application()
app.master.title("Backtracking Maze")
app.mainloop()
