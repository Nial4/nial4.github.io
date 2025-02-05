---
layout: post
title: 'BitmapTOimageSource的用法'
tags: [C#, promgramming]
comments: true
---

Imaging 库的方法

```csharp
 public static BitmapSource ChangeBitmapToBitmapSource(this Bitmap bmp)
        {
            BitmapSource returnSource;
            try
            {
                returnSource = Imaging.CreateBitmapSourceFromHBitmap(bmp.GetHbitmap(), IntPtr.Zero, Int32Rect.Empty, BitmapSizeOptions.FromEmptyOptions());
            }
            catch
            {
                returnSource = null;
            }
            return returnSource;
        }
```
