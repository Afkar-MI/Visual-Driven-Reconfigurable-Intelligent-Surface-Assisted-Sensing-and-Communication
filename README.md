# Visual-Driven-Reconfigurable-Intelligent-Surface-Assisted-Sensing-and-Communication
## Algorithm

**Algorithm 1: YOLOv7-Based Real-Time User Detection**  
1. **INPUT:** YOLOv7 model weights, RealSense camera streams, UART parameters, Pattern data.  
2. **Configure** Intel RealSense D435i Camera: Enable RGB and depth streams.  
3. **Load** YOLOv7-tiny model pretrained on COCO dataset. Set confidence threshold τ₍conf₎ = 0.7, IoU threshold for NMS θ₍nms₎ = 0.7.  
4. **Initialize** UART communication. Load RIS reflection patterns from "Pattern.txt".  
5. **Set** last Grid Position = None *(keep record of user last grid position)*  
6. **While** (1) *(keep user detection)*  
7. &emsp; **Capture** and align color and depth frames from the camera.  
8. &emsp; **Preprocess** RGB frame: Resize to 640×640, normalize pixel values to [0, 1], convert to PyTorch tensor.  
9. &emsp; **Run** YOLOv7 inference to detect objects. Let _detections_ = {(x₁, y₁, x₂, y₂, class, confidence)}.  
10. &emsp; **Apply** Non-Maximum Suppression (NMS) to filter overlapping detections.  
11. &emsp; **Calculate** the column widht by divide the image frame  width into 8 : columnWidth = frameWidth / 8.  
12. &emsp; **For each** detection:  
13. &emsp;&emsp; **If** (class == person **and** confidence ≥ τ₍conf₎):  
14. &emsp;&emsp;&emsp; **Calculate** the bounding box center (Cₓ, Cᵧ) *(Cₓ = (x₁ + x₂)/2, Cᵧ = (y₁ + y₂)/2)*  
15. &emsp;&emsp;&emsp; **Determine** the grid column: column = int(Cₓ / columnWidth);  
16. &emsp;&emsp;&emsp; **Set** currentGridPosition = (0, column) *(determine which grid area the user is in)*  
17. &emsp;&emsp;&emsp; **If** (currentGridPosition ≠ lastGridPosition) *(check if the UE moves to a new grid cell)*:  
18. &emsp;&emsp;&emsp;&emsp; **Update** lastGridPosition = currentGridPosition;  
19. &emsp;&emsp;&emsp;&emsp; **Set** pattern = patterns[column];  
20. &emsp;&emsp;&emsp;&emsp; **Send** pattern to RIS control board via UART.write(pattern);  
21. **End While**
