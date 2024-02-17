---
title: Endless Runner
seo_title: Endless Runner
summary: This is the game inspired by Subway Surfer.
description: This is the game inspired by Subway Surfer.
slug: endless-runner
author: Himanshu

draft: false
date: 2019-02-20T03:52:30-05:00
lastmod: 
expiryDate: 
publishDate: 

feature_image: runner.png
feature_video: videos/runner.mp4
feature_image_alt: Endless Runner

project types: 
    - Personal

techstack:
    - Unity
    - GIMP
live_url: https://chungununo1.itch.io/endless-runner-game
source_url: https://github.com/Heemo2000/Runner-Game
---

This is the game inspired by Subway Surfer. In this game, player takes control of the cat and has to collect the coins while avoiding
obstacles in the way.

Here are the technical details of the game:

* Implemented mario style jumping mechanic.

* Implemented path generation based on sliding window.

* Implemented various types of bullets.

* Implemented object pooling to stop memory wastage.


## Code Blocks

### Code block for path generation logic

```C#
void Update()
{
    Floor firstFloor = _floors[0];
    Transform firstFloorEndPoint = firstFloor.EndPoint;
    float offset = player.transform.position.z - firstFloorEndPoint.position.z;
    if(offset >= spawnDistance)
    {
        Debug.Log("Generating path");
        for(int i = 1; i <= floorsGenerationCount; i++)
        {
            Floor lastFloor = _floors[_floors.Count - 1];
            Transform lastFloorEndPoint = lastFloor.EndPoint;
            
            Floor generatedFloor = PoolManager.Instance.ReuseObject(floor.gameObject,lastFloorEndPoint.position,Quaternion.identity)PoolObject as Floor;
            _floors.Add(generatedFloor);
        
            Transform generatedFloorEndPoint = generatedFloor.EndPoint;
            if(generateObstacles)
            {
                GenerateObstacles(generatedFloor,generatedFloorEndPoint);
            }
            if(generateCoins)
            {
                GenerateCoins(generatedFloor,generatedFloorEndPoint);
            }
        }
        
        SetToOrigin();
        _floors.Remove(firstFloor);
        StartCoroutine(DestroyFloor(firstFloor,floorDestroyTime));       
    }
}
void SetToOrigin()
{
    playerCamera.enabled = false;
    player.transform.position -= Vector3.forward * backwardDistance;
    playerCamera.enabled = true;
    foreach(Floor floor in _floors)
    {
        floor.transform.position -= Vector3.forward * backwardDistance;
        List<PoolObject> things = floor.ThingsOnFloor;
        foreach(PoolObject thing in things)
        {
            if(thing != null)
            {
                thing.transform.position -= Vector3.forward * backwardDistance;
            }
        }
    }
}
```


### Code block for player sliding logic.

```C#
public IEnumerator Slide(float input)
{
    _isSliding = true;
    float initialPositionX = _playerRB.transform.position.x;
    float finalPositionX = _playerRB.transform.position.x + input * slideDistance;
    _currentPositionX = initialPositionX;
    float distance = 0;
    while(distance < slideDistance)
    {
        _currentPositionX = Mathf.Lerp(initialPositionX,finalPositionX,distance);
        //Debug.Log("Slide distance covered : " + distance);
        distance += slideSpeed * Time.deltaTime;
        yield return null;
    }
    _currentPositionX = finalPositionX; 
    _isSliding = false;
}

```