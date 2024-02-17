---
title: Space Shooter
seo_title: Space Shooter
summary: This is the arcade game inspired by the classic Space Impact.
description: This is the arcade game inspired by the classic Space Impact.
slug: space-shooter
author: Himanshu

draft: false
date: 2019-02-20T03:52:30-05:00
lastmod: 
expiryDate: 
publishDate: 

feature_image: space_shooter.png
feature_video: videos/space_shooter.mp4
feature_image_alt: Endless Runner

project types: 
    - Personal

techstack:
    - Unity
    - GIMP
live_url: https://chungununo1.itch.io/space-shooter
source_url: https://github.com/Heemo2000/Space-Shooter
---

This is the arcade game inspired by the classic Space Impact.In this, player takes the control of spacecraft and has to kill
enemies coming in front while surviving. 

Here are the technical details of the game:

* Implemented spacecraft movement.

* Implemented object pooling to stop memory wastage.

* Implemented enemy generation.

## Code Blocks

### Code block for object pooling logic

```C#
public class Pool
{
    private Queue<PoolObject> _pooledObjects;

    public Pool(PoolObjectData poolObjectData,Transform parentTransform = null)
    {
        _pooledObjects = new Queue<PoolObject>();
        GameObject poolObjectsContainer = new GameObject(poolObjectData.actualObject.name + " Pool");
        if(parentTransform != null)
        {
            poolObjectsContainer.transform.parent = parentTransform;
        }
        for(int i = 0; i < poolObjectData.count; i++)
        {
            PoolObject poolObject = GameObject.Instantiate<PoolObject>(poolObjectData.actualObject);
            poolObject.Pool = this;
            poolObject.transform.parent = poolObjectsContainer.transform;
            poolObject.gameObject.SetActive(false);
            _pooledObjects.Enqueue(poolObject);    
        }
    }
    

    public PoolObject ReuseObject(Vector3 position,Quaternion rotation)
    {
        if(_pooledObjects.Count == 0)
        {
            return null;
        }
        PoolObject poolObject = _pooledObjects.Dequeue();
        poolObject.transform.position = position;
        poolObject.transform.rotation = rotation;
        poolObject.gameObject.SetActive(true);
        poolObject.Reuse();
        return poolObject;
    }

    public void ReturnObjectToPool(PoolObject poolObject)
    {
        if(poolObject == null)
        {
            Debug.LogError("Null object cannot be returned to pool!!");
            return;
        }

        if(!poolObject.Pool.Equals(this))
        {
            Debug.LogError(poolObject.name + " Pool not found!!");
            return;
        }

        _pooledObjects.Enqueue(poolObject);
    }
}

```

```C#

public abstract class PoolObject : MonoBehaviour
{
    private Pool _pool;

    public Pool Pool { get => _pool; set => _pool = value;}
    public abstract void Reuse();

    
    protected virtual void Destroy()
    {
        this.gameObject.SetActive(false);
        _pool.ReturnObjectToPool(this);
    }
}

```