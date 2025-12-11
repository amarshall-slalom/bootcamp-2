# Functional Requirements - TODO Application

## Overview
This document outlines the functional requirements for a simple TODO application built with React frontend and Node.js/Express backend.

## Functional Requirements

### Basic TODO Item Management

**FR-1: Create TODO Item**
- User shall be able to enter text in an input field to create a new TODO item
- Upon submission (Enter key or button click), the item shall be added to the current list
- The input field shall clear after the item is created

**FR-2: Display TODO Items**
- User shall be able to view all TODO items in the current list
- Each TODO item shall display its text content
- Each TODO item shall display its completion status

**FR-3: Mark TODO Item as Complete**
- User shall be able to check/uncheck a TODO item to toggle its completion status
- Completed items shall be visually distinguished from incomplete items (e.g., strikethrough text, different color)

**FR-4: Delete TODO Item**
- User shall be able to delete a TODO item from the list
- System shall remove the item permanently from the current list
- User shall receive visual confirmation of the deletion

### Advanced List Management

**FR-5: Create New TODO List**
- User shall be able to create a new, empty TODO list
- User shall be able to provide a name for the new list
- The system shall switch to the newly created list after creation

**FR-6: View All Lists**
- User shall be able to view all available TODO lists
- Each list shall display its name and optionally the number of items

**FR-7: Switch Between Lists**
- User shall be able to navigate between different TODO lists
- When switching lists, the application shall display the items belonging to the selected list
- The current list shall be clearly indicated to the user

**FR-8: Persist Data**
- All TODO items and lists shall be persisted in the backend
- Data shall persist across application restarts
- Changes shall be synchronized between frontend and backend
