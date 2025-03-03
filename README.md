# Visual-Driven-Reconfigurable-Intelligent-Surface-Assisted-Sensing-and-Communication
## Algorithm

1. **Algorithm 1: YOLOv7-Based Real-Time User Detection**  
2. **INPUT:** YOLOv7 model weights, RealSense camera streams, UART parameters, Pattern data.  
3. **Configure** Intel RealSense D435i Camera: Enable RGB and depth streams.  
4. **Load** YOLOv7-tiny model pretrained on COCO dataset. Set confidence threshold τ₍conf₎ = 0.7, IoU threshold for NMS θ₍nms₎ = 0.7.  
5. **Initialize** UART communication. Load RIS reflection patterns from "Pattern.txt".  
6. **Set** last Grid Position = None *(keep record of user last grid position)*  
7. **While** (1) *(keep user detection)*  
8. &emsp; **Capture** and align color and depth frames from the camera.  
9. &emsp; **Preprocess** RGB frame: Resize to 640×640, normalize pixel values to [0, 1], convert to PyTorch tensor.  
10. &emsp; **Run** YOLOv7 inference to detect objects. Let _detections_ = {(x₁, y₁, x₂, y₂, class, confidence)}.  
11. &emsp; **Apply** Non-Maximum Suppression (NMS) to filter overlapping detections.  
12. &emsp; **Divide** the image frame width into 8 equal grid columns: columnWidth = frameWidth / 8.  
13. &emsp; **For each** detection:  
14. &emsp;&emsp; **If** (class == person **and** confidence ≥ τ₍conf₎):  
15. &emsp;&emsp;&emsp; **Calculate** the bounding box center (Cₓ, Cᵧ) *(Cₓ = (x₁ + x₂)/2, Cᵧ = (y₁ + y₂)/2)*  
16. &emsp;&emsp;&emsp; **Determine** the grid column: column = int(Cₓ / columnWidth);  
17. &emsp;&emsp;&emsp; **Set** currentGridPosition = (0, column) *(determine which grid area the user is in)*  
18. &emsp;&emsp;&emsp; **If** (currentGridPosition ≠ lastGridPosition) *(check if the UE moves to a new grid cell)*:  
19. &emsp;&emsp;&emsp;&emsp; **Update** lastGridPosition = currentGridPosition;  
20. &emsp;&emsp;&emsp;&emsp; **Set** pattern = patterns[column];  
21. &emsp;&emsp;&emsp;&emsp; **Send** pattern to RIS control board via UART.write(pattern);  
22. **End While**
