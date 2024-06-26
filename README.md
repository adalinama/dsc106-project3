# DSC106 Project 3 Write-Up
Contributors:  Adalina Ma, Mark Lee, Eric Stratford

## Table of Contents
- [Introduction](#introduction)
- [Design Rationale](#design-rationale)
- [Adalina(Map Design)](#adalina)
- [Mark (Visual Design)](#mark)
- [Eric (Fine Tuning)](#eric)
- [Closing](#closing)


## Introduction

Our aim for this project was to create an interactive map visualization displaying the internet usage percentages by country across specifiable years. We wanted the map itself to statically convey an overview of the internet usage variance along with an additional insight interaction from hovering over specific countries. In developing this interactive visualization, we made a series of deliberate design decisions to ensure a compelling user experience and effective data communication.

## Design Rationale
First and foremost, we decided to use D3.js and a natural earth projection to depict the global map. This decision was made with the intention of giving people a recognizable and easy-to-use representation of global geography, which would aid in their comprehension of how internet usage is distributed among various nations. To encode percentages of internet usage, we used a color scale that went from blue to white in order. White denotes neutrality and clarity, while blue, which is frequently connected to technology and communication, was selected to represent higher internet usage percentages. Users can readily understand regional differences in internet consumption thanks to this color scheme. In terms of interaction, we implemented mouseover events to trigger tooltips displaying detailed information about each country's internet usage percentage. This interactive feature enhances user engagement and facilitates exploration of specific data points, fostering a deeper understanding of the dataset. We gave users buttons to choose from a variety of years, and the visualization updated dynamically in response, enabling temporal analysis. This feature increases the application's analytical utility by enabling users to track changes in internet usage over time. By properly designing the components with CSS, we were able to create a cohesive user experience throughout the whole development process. Typography, color palettes, and layout design were all carefully considered to create an interface that is aesthetically pleasing and promotes user involvement.

## Adalina 
On the map side, Adalina took charge of the hovering features, mapping the dataset to the map, and making sure the buttons matched the right dataset. I would estimate that the development time took about 15 to 20 hours spread over 5 days. Among the many facets, fundamental chores like configuring the SVG container and projecting the map called for close attention to detail and a working knowledge of D3.js features. Adalina's concentration on this field was essential to guaranteeing the global map's correct depiction and the dataset's smooth integration. She also implemented the code to connect the datasets we used to reflect the information on the map, including the functions of hovering over the countries as well as changing the data according to which year button is pressed.Another important aspect to our design was to guarantee a seamless user experience and simple interaction with the visualization. Adalina took upon implementing tooltip functionality and interaction management. Adalina's attention to these details made sure that consumers could quickly obtain more details about each nation and get interactive visual cues upon hovering over each country. Additionally, Adalina had to cope with responsive design, which required extra work to dynamically modify the SVG container and map projection across various screen sizes. The design and functionality of the visualization were refined through iterative refinement and the incorporation of user feedback throughout the development process. 

## Mark
Through the development of our interactive map, Mark’s contributions focused on increasing the interpretability and functionality of the site, as well as assisting with general aesthetic improvements of the site. A primary role was taken in data cleaning and filtering, ensuring that our dataset containing internet usage percentage by country was organized in a way that was easy to incorporate into our map. This included querying data by year to separate our dataset into 7 different .csv files and manually removing/altering irrelevant data that were initially included in the dataset. Mark also contributed to developing the style of the buttons with a focus on keeping them simple yet visually appealing and fitting with the color scheme of the map data. The buttons serve as one of the main key features of interactivity in this project, and by incorporating elements like shadows and hovering, user experience can improve with the well-integrated designs. Additionally, Mark created an intuitive gradient legend in the bottom left to assist readers with interpretation of our data. This not only leads to improved user engagement with the data but also allows the user to observe and predict trends in internet usage data themselves.

## Eric
Eric primarily took up a supporting role in each area of the project, helping Adalina and Mark with design decisions on the direction we wanted to take our visualization while concurrently building the initial program. He ensured visual continuity and clarity, making sure that the map was easy to read through deliberation color and stroke selection. Moreover, he helped build out the year changing system, giving the user a clear picture of the selections. He also worked with Mark, creating the data mapping between countries, such that the internet usage data was correctly aligned to the countries used for the map.

## Closing
In summary, our development process involved meticulous decision-making to create an interactive web application that effectively communicates global internet usage trends and distributions.


