local Event = game.ReplicatedStorage:WaitForChild("CombatRemote"):WaitForChild("Combat")
local CombatFolder =  script:WaitForChild("Combat")
local BlockingEvent =  game.ReplicatedStorage.IsGaurdingEvent
local TS = game:GetService("TweenService")


local soundFolder = CombatFolder.Sound
local Animations = CombatFolder.Animation
local attackAnim = Animations.Attack
local EnemyAnims = Animations.Enemy


local eAnims =  {
	EnemyAnims:WaitForChild("Hit"),
	EnemyAnims:WaitForChild("KnockBack"),
}
local anims =  {
	attackAnim:WaitForChild("1"),
	attackAnim:WaitForChild("2"),
	attackAnim:WaitForChild("3"),
	attackAnim:WaitForChild("4"),
	attackAnim:WaitForChild("5"),
	attackAnim:WaitForChild("5(air)"),
	attackAnim:WaitForChild("4(air)")
}

local EffectFolder = CombatFolder.Effect

local Effects = {
	"filler",
	EffectFolder:WaitForChild("HitEffect"),
	EffectFolder:WaitForChild("BEAM"),
	EffectFolder:WaitForChild("Wave"),
	EffectFolder:WaitForChild("Ball"),
	"Filler"
}


local  Sounds = {
	soundFolder:WaitForChild("SwingFist"),
	soundFolder:WaitForChild("Punched"),
	soundFolder:waitForChild("FinalPunched"),
	"filler",
	"filler",
	soundFolder:WaitForChild("Heavy")
}



local dmg = 4
local WaitTime = .05
local hitboxtime = .25

Event.OnServerEvent:Connect(function(player,count,canAir,State)
	local CanAttack =  player.Character:FindFirstChild("CanAttack")
	local char = player.Character
	local hum =  char:WaitForChild("Humanoid")
	local Humrp = char:WaitForChild("HumanoidRootPart")

	local function shakeCamera(person,Magnitude,Roughness,FadeIn,FadeOut,Lenght)
		game.ReplicatedStorage.CamShake:FireClient(person,Magnitude,Roughness,FadeIn,FadeOut,Lenght)
	end


	if CanAttack.Value == true and char:FindFirstChild("eStun")== nil and char:FindFirstChild("Stun")== nil then



		----------------------------------------------- Before Punch
		local Swing = Sounds[1]:Clone()
		Swing.Parent = char.Torso
		Swing:Play()	
		game.Debris:AddItem(Swing,.5674)

		if count == 4 and canAir == false then
			hum:LoadAnimation(anims[count]):Play() 
			wait(WaitTime)			
			local hitbox =  script.HitBox:Clone()
			hitbox.Parent = char

			hitbox.CFrame =  char.HumanoidRootPart.CFrame + char.HumanoidRootPart.CFrame.LookVector*2
			hitbox.Orientation = char.HumanoidRootPart.Orientation + Vector3.new(0,-90,0)
			local weld =  Instance.new("WeldConstraint")
			weld.Part0 = char.HumanoidRootPart
			weld.Part1 =  hitbox
			weld.Parent =  weld.Part0

			local debounce = false 

			hitbox.Touched:Connect(function(hit)
				if hit.Parent ~= char and hit.Parent:IsA("Model")and hit.Parent:FindFirstChild("Humanoid")then
					if debounce == false then
						debounce = true
						hitbox:Destroy()
						local eChar =  hit.Parent
						local eHumrp = eChar:FindFirstChild("HumanoidRootPart")
						local eHum =  eChar:FindFirstChild("Humanoid")
						
						if eChar:FindFirstChild("HitBox")then
							game.Debris:AddItem(eChar:FindFirstChild("HitBox"),0)
						end
						
						if eChar:FindFirstChild("IsBlocking")then

							
							BlockingEvent:Fire(eChar,count)
							

						else
							local HitEffect = Effects[2]:Clone()
							HitEffect.Parent =  eHumrp	
							HitEffect:Emit(1)
							game.Debris:AddItem(HitEffect,.5)								
							---------------------------------------------------------------------------------
							 eHum:TakeDamage(dmg)
							local Hit = eHum:LoadAnimation(eAnims[1])
							Hit:Play()

							local Punched = Sounds[2]:Clone()
							Punched.Parent = eHum.Parent.HumanoidRootPart
							Punched:Play()
							game.Debris:AddItem(Punched,.307)
							-----------------------------------------------------------
							local m1STUNT = Instance.new("BoolValue")
							m1STUNT.Name = "eStun"
							m1STUNT.Parent = eChar
							game.Debris:AddItem(m1STUNT,.7)

							for i,v in pairs(eHumrp:GetChildren())do
								if v.Name == "SmallMoveVel"then
									v:Destroy()
								end
							end

							for i,v in pairs(Humrp:GetChildren())do
								if v.Name == "MoveMe"then
									v:Destroy()
								end
							end

							if count == 2 then
								local MoveVel1 =  Instance.new("BodyVelocity")
								MoveVel1.MaxForce =  Vector3.new(math.huge,math.huge,math.huge)
								MoveVel1.Parent =  Humrp
								MoveVel1.Velocity = Humrp.Parent.HumanoidRootPart.CFrame.LookVector * 8
								MoveVel1.Name = "MoveMe"
								game.Debris:AddItem(MoveVel1,.5)

								local vel = Instance.new("BodyVelocity")
								vel.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
								vel.Parent = eHumrp
								vel.Velocity = Humrp.CFrame.LookVector * 1
								vel.Name  =  "SmallMoveVel"
								game.Debris:AddItem(vel,.7)
							else
								--local MoveVel1 =  Instance.new("BodyVelocity")
								--MoveVel1.MaxForce =  Vector3.new(math.huge,math.huge,math.huge)
								--MoveVel1.Parent =  Humrp
								--MoveVel1.Velocity = Humrp.Parent.HumanoidRootPart.CFrame.LookVector * 8
								--MoveVel1.Name = "MoveMe"
								--game.Debris:AddItem(MoveVel1,.2)

								local vel = Instance.new("BodyVelocity")
								vel.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
								vel.Parent = eHumrp
								vel.Velocity = Humrp.CFrame.LookVector * 1
								vel.Name  =  "SmallMoveVel"
								game.Debris:AddItem(vel,.7)
							end
						end
					end
				end
			end)
			game.Debris:AddItem(weld,hitboxtime)
			game.Debris:AddItem(hitbox,hitboxtime)
			debounce = false

		elseif count == 4 and canAir == true then

			hum:LoadAnimation(anims[7]):Play()

			wait(WaitTime)			
			local hitbox =  script.HitBox:Clone()
			hitbox.Parent = char

			hitbox.CFrame =  char.HumanoidRootPart.CFrame + char.HumanoidRootPart.CFrame.LookVector*2
			hitbox.Orientation = char.HumanoidRootPart.Orientation + Vector3.new(0,-90,0)
			local weld =  Instance.new("WeldConstraint")
			weld.Part0 = char.HumanoidRootPart
			weld.Part1 =  hitbox
			weld.Parent =  weld.Part0

			local debounce = false 

			hitbox.Touched:Connect(function(hit)
				if hit.Parent ~= char and hit.Parent:IsA("Model")and hit.Parent:FindFirstChild("Humanoid")then
					if debounce == false then
						debounce = true
						hitbox:Destroy()
						local eChar =  hit.Parent
						local eHumrp = eChar:FindFirstChild("HumanoidRootPart")
						local eHum =  eChar:FindFirstChild("Humanoid")
	
						if eChar:FindFirstChild("HitBox")then
							game.Debris:AddItem(eChar:FindFirstChild("HitBox"),0)
						end
						
						if eChar:FindFirstChild("IsBlocking") then
							
							BlockingEvent:Fire(eChar,count)

						else

							local HitEffect = Effects[2]:Clone()
							HitEffect.Parent =  eHumrp	
							HitEffect:Emit(1)
							game.Debris:AddItem(HitEffect,.5)								
							---------------------------------------------------------------------------------
							eHum:TakeDamage(dmg)
							local Hit = eHum:LoadAnimation(eAnims[1])
							Hit:Play()


							local Punched = Sounds[2]:Clone()
							Punched.Parent = eHum.Parent.HumanoidRootPart
							Punched:Play()
							game.Debris:AddItem(Punched,.307)
							-----------------------------------------------------------
							local m1STUNT = Instance.new("BoolValue")
							m1STUNT.Name = "eStun"
							m1STUNT.Parent = eChar
							game.Debris:AddItem(m1STUNT,.7)

							for i,v in pairs(eHumrp:GetChildren())do
								if v.Name == "SmallMoveVel"then
									v:Destroy()
								end
							end

							for i,v in pairs(Humrp:GetChildren())do
								if v.Name == "MoveMe"then
									v:Destroy()
								end
							end

							local MoveVel1 =  Instance.new("BodyVelocity")
							MoveVel1.MaxForce =  Vector3.new(math.huge,math.huge,math.huge)
							MoveVel1.Parent =  Humrp
							MoveVel1.Velocity = Humrp.Parent.HumanoidRootPart.CFrame.upVector * 16
							MoveVel1.Name = "MoveMe"
							game.Debris:AddItem(MoveVel1,.7)



							eHumrp.CFrame = CFrame.new((Humrp.CFrame * CFrame.new(0,0,-4)).p,Humrp.CFrame.p)

							local vel = Instance.new("BodyVelocity")
							vel.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
							vel.Parent = eHumrp
							vel.Velocity = eHumrp.CFrame.upVector * 20
							vel.Name  =  "SmallMoveVel"
							game.Debris:AddItem(vel,.7)
						end
					end
				end
			end)
			game.Debris:AddItem(weld,hitboxtime)
			game.Debris:AddItem(hitbox,hitboxtime)
			debounce = false
		elseif count == 3 then
			hum:LoadAnimation(anims[count]):Play() 
			wait(WaitTime)			
			local hitbox =  script.HitBox:Clone()
			hitbox.Parent = char

			hitbox.CFrame =  char.HumanoidRootPart.CFrame + char.HumanoidRootPart.CFrame.LookVector*2
			hitbox.Orientation = char.HumanoidRootPart.Orientation + Vector3.new(0,-90,0)
			local weld =  Instance.new("WeldConstraint")
			weld.Part0 = char.HumanoidRootPart
			weld.Part1 =  hitbox
			weld.Parent =  weld.Part0

			local debounce = false 

			hitbox.Touched:Connect(function(hit)
				if hit.Parent ~= char and hit.Parent:IsA("Model")and hit.Parent:FindFirstChild("Humanoid")then
					if debounce == false then
						debounce = true
						hitbox:Destroy()
						local eChar =  hit.Parent
						local eHumrp = eChar:FindFirstChild("HumanoidRootPart")
						local eHum =  eChar:FindFirstChild("Humanoid")
						
						if eChar:FindFirstChild("HitBox")then
							game.Debris:AddItem(eChar:FindFirstChild("HitBox"),0)
						end
						
						if eChar:FindFirstChild("IsBlocking") then

							BlockingEvent:Fire(eChar,count)
						else
							local HitEffect = Effects[2]:Clone()
							HitEffect.Parent =  eHumrp	
							HitEffect:Emit(1)
							game.Debris:AddItem(HitEffect,.5)								
							---------------------------------------------------------------------------------
							eHum:TakeDamage(dmg)
							local Hit = eHum:LoadAnimation(eAnims[1])
							Hit:Play()

							local Punched = Sounds[2]:Clone()
							Punched.Parent = eHum.Parent.HumanoidRootPart
							Punched:Play()
							game.Debris:AddItem(Punched,.307)
							-----------------------------------------------------------
							local m1STUNT = Instance.new("BoolValue")
							m1STUNT.Name = "eStun"
							m1STUNT.Parent = eChar
							game.Debris:AddItem(m1STUNT,.7)

							for i,v in pairs(eHumrp:GetChildren())do
								if v.Name == "SmallMoveVel"then
									v:Destroy()
								end
							end

							for i,v in pairs(Humrp:GetChildren())do
								if v.Name == "MoveMe"then
									v:Destroy()
								end
							end

							local MoveVel1 =  Instance.new("BodyVelocity")
							MoveVel1.MaxForce =  Vector3.new(math.huge,math.huge,math.huge)
							MoveVel1.Parent =  Humrp
							MoveVel1.Velocity = Humrp.Parent.HumanoidRootPart.CFrame.LookVector * 8
							MoveVel1.Name = "MoveMe"
							game.Debris:AddItem(MoveVel1,.5)

							local vel = Instance.new("BodyVelocity")
							vel.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
							vel.Parent = eHumrp
							vel.Velocity = Humrp.CFrame.LookVector * 1
							vel.Name  =  "SmallMoveVel"
							game.Debris:AddItem(vel,.7)

						end

					end
				end
			end)
			game.Debris:AddItem(weld,hitboxtime)
			game.Debris:AddItem(hitbox,hitboxtime)
			debounce = false	
		elseif count == 2 then
			hum:LoadAnimation(anims[count]):Play() 
			wait(WaitTime)			
			local hitbox =  script.HitBox:Clone()
			hitbox.Parent = char

			hitbox.CFrame =  char.HumanoidRootPart.CFrame + char.HumanoidRootPart.CFrame.LookVector*2
			hitbox.Orientation = char.HumanoidRootPart.Orientation + Vector3.new(0,-90,0)
			local weld =  Instance.new("WeldConstraint")
			weld.Part0 = char.HumanoidRootPart
			weld.Part1 =  hitbox
			weld.Parent =  weld.Part0

			local debounce = false 

			hitbox.Touched:Connect(function(hit)
				if hit.Parent ~= char and hit.Parent:IsA("Model")and hit.Parent:FindFirstChild("Humanoid")then
					if debounce == false then
						debounce = true
						hitbox:Destroy()
						local eChar =  hit.Parent
						local eHumrp = eChar:FindFirstChild("HumanoidRootPart")
						local eHum =  eChar:FindFirstChild("Humanoid")
						
						if eChar:FindFirstChild("HitBox")then
							game.Debris:AddItem(eChar:FindFirstChild("HitBox"),0)
						end
						
						if eChar:FindFirstChild("IsBlocking") then

							BlockingEvent:Fire(eChar,count)
						else
							local HitEffect = Effects[2]:Clone()
							HitEffect.Parent =  eHumrp	
							HitEffect:Emit(1)
							game.Debris:AddItem(HitEffect,.5)								
							---------------------------------------------------------------------------------
							eHum:TakeDamage(dmg)
							local Hit = eHum:LoadAnimation(eAnims[1])
							Hit:Play()

							local Punched = Sounds[2]:Clone()
							Punched.Parent = eHum.Parent.HumanoidRootPart
							Punched:Play()
							game.Debris:AddItem(Punched,.307)
							-----------------------------------------------------------
							local m1STUNT = Instance.new("BoolValue")
							m1STUNT.Name = "eStun"
							m1STUNT.Parent = eChar
							game.Debris:AddItem(m1STUNT,.7)

							for i,v in pairs(eHumrp:GetChildren())do
								if v.Name == "SmallMoveVel"then
									v:Destroy()
								end
							end

							for i,v in pairs(Humrp:GetChildren())do
								if v.Name == "MoveMe"then
									v:Destroy()
								end
							end

							--local MoveVel1 =  Instance.new("BodyVelocity")
							--MoveVel1.MaxForce =  Vector3.new(math.huge,math.huge,math.huge)
							--MoveVel1.Parent =  Humrp
							--MoveVel1.Velocity = Humrp.Parent.HumanoidRootPart.CFrame.LookVector * 12
							--MoveVel1.Name = "MoveMe"
							--game.Debris:AddItem(MoveVel1,.2)

							local vel = Instance.new("BodyVelocity")
							vel.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
							vel.Parent = eHumrp
							vel.Velocity = Humrp.CFrame.LookVector * 1
							vel.Name  =  "SmallMoveVel"
							game.Debris:AddItem(vel,.7)

						end

					end
				end
			end)
			game.Debris:AddItem(weld,hitboxtime)
			game.Debris:AddItem(hitbox,hitboxtime)
			debounce = false
		elseif count == 1 then
			hum:LoadAnimation(anims[count]):Play()

			wait(WaitTime)			
			local hitbox =  script.HitBox:Clone()
			hitbox.Parent = char

			hitbox.CFrame =  char.HumanoidRootPart.CFrame + char.HumanoidRootPart.CFrame.LookVector*2
			hitbox.Orientation = char.HumanoidRootPart.Orientation + Vector3.new(0,-90,0)
			local weld =  Instance.new("WeldConstraint")
			weld.Part0 = char.HumanoidRootPart
			weld.Part1 =  hitbox
			weld.Parent =  weld.Part0

			local debounce = false 

			hitbox.Touched:Connect(function(hit)
				if hit.Parent ~= char and hit.Parent:IsA("Model")and hit.Parent:FindFirstChild("Humanoid")then
					if debounce == false then
						debounce = true
						hitbox:Destroy()
						local eChar =  hit.Parent
						local eHumrp = eChar:FindFirstChild("HumanoidRootPart")
						local eHum =  eChar:FindFirstChild("Humanoid")
						
						if eChar:FindFirstChild("HitBox")then
							game.Debris:AddItem(eChar:FindFirstChild("HitBox"),0)
						end
						
						if eChar:FindFirstChild("IsBlocking") then

							BlockingEvent:Fire(eChar,count)
							
						else
							local HitEffect = Effects[2]:Clone()
							HitEffect.Parent =  eHumrp	
							HitEffect:Emit(1)
							game.Debris:AddItem(HitEffect,.5)								
							---------------------------------------------------------------------------------
							eHum:TakeDamage(dmg)
							local Hit = eHum:LoadAnimation(eAnims[1])
							Hit:Play()

							local Punched = Sounds[2]:Clone()
							Punched.Parent = eHum.Parent.HumanoidRootPart
							Punched:Play()
							game.Debris:AddItem(Punched,.307)
							-----------------------------------------------------------
							local m1STUNT = Instance.new("BoolValue")
							m1STUNT.Name = "eStun"
							m1STUNT.Parent = eChar
							game.Debris:AddItem(m1STUNT,.7)

							for i,v in pairs(eHumrp:GetChildren())do
								if v.Name == "SmallMoveVel"then
									v:Destroy()
								end
							end

							for i,v in pairs(Humrp:GetChildren())do
								if v.Name == "MoveMe"then
									v:Destroy()
								end
							end

							--local MoveVel1 =  Instance.new("BodyVelocity")
							--MoveVel1.MaxForce =  Vector3.new(math.huge,math.huge,math.huge)
							--MoveVel1.Parent =  Humrp
							--MoveVel1.Velocity = Humrp.Parent.HumanoidRootPart.CFrame.LookVector * 12
							--MoveVel1.Name = "MoveMe"
							--game.Debris:AddItem(MoveVel1,.2)

							local vel = Instance.new("BodyVelocity")
							vel.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
							vel.Parent = eHumrp
							vel.Velocity = Humrp.CFrame.LookVector * 1
							vel.Name  =  "SmallMoveVel"
							game.Debris:AddItem(vel,.7)

						end

					end
				end
			end)
			game.Debris:AddItem(weld,hitboxtime)
			game.Debris:AddItem(hitbox,hitboxtime)
			debounce = false
		elseif count == 5 and State ~=  Enum.HumanoidStateType.Freefall then	
			hum:LoadAnimation(anims[count]):Play()


			wait(WaitTime)			
			local hitbox =  script.HitBox:Clone()
			hitbox.Parent = char

			hitbox.CFrame =  char.HumanoidRootPart.CFrame + char.HumanoidRootPart.CFrame.LookVector*2
			hitbox.Orientation = char.HumanoidRootPart.Orientation + Vector3.new(0,-90,0)
			local weld =  Instance.new("WeldConstraint")
			weld.Part0 = char.HumanoidRootPart
			weld.Part1 =  hitbox
			weld.Parent =  weld.Part0

			local debounce = false 

			hitbox.Touched:Connect(function(hit)
				if hit.Parent ~= char and hit.Parent:IsA("Model")and hit.Parent:FindFirstChild("Humanoid")then
					if debounce == false then
						debounce = true
						hitbox:Destroy()
						local eChar =  hit.Parent
						local eHumrp = eChar:FindFirstChild("HumanoidRootPart")
						local eHum =  eChar:FindFirstChild("Humanoid")
						
						if eChar:FindFirstChild("HitBox")then
							game.Debris:AddItem(eChar:FindFirstChild("HitBox"),0)
						end
						
						shakeCamera(player,2,15,1,1,.5)
						if eChar:FindFirstChild("IsBlocking")then
							
							BlockingEvent:Fire(eChar,count,char)
						else
							for i,v in pairs(Humrp:GetChildren())do
								if v.Name == "MoveMe"then
									v:Destroy()
								end
							end
							for i,v in pairs(eHumrp:GetChildren())do
								if v.Name == "SmallMoveVel"then
									v:Destroy()
								end
							end

							--local MoveVel1 =  Instance.new("BodyVelocity")
							--MoveVel1.MaxForce =  Vector3.new(math.huge,math.huge,math.huge)
							--MoveVel1.Parent =  Humrp
							--MoveVel1.Velocity = Humrp.Parent.HumanoidRootPart.CFrame.LookVector * 2
							--MoveVel1.Name = "MoveMe"
							--game.Debris:AddItem(MoveVel1,.4)

							if game.Players:GetPlayerFromCharacter(eChar) then
								local eP =  game.Players:GetPlayerFromCharacter(eChar)
								shakeCamera(eP,4,20,1,1,1)

							end

							local HitEffect = Effects[2]:Clone()
							HitEffect.Parent =  eHumrp	
							HitEffect.Color = ColorSequence.new {ColorSequenceKeypoint.new(0,Color3.fromRGB(255, 0, 0)),ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 0, 0))}
							HitEffect:Emit(1)
							game.Debris:AddItem(HitEffect,.5)	

							-----------------"Location"
							wait(.05)

							-------------------------------------------------------------------------------------------
							for i,v in pairs (eHumrp:GetChildren())do
								if v:IsA("BodyVelocity")then 
									v:destroy()
								end	
							end





							local PushVel =  Instance.new("BodyVelocity",eHumrp)
							PushVel.Velocity = Humrp.CFrame.LookVector*50
							PushVel.MaxForce =  Vector3.new(math.huge,math.huge,math.huge)
							game.Debris:AddItem(PushVel,.4)


							-----------------------------------------------------------
							local m1STUNT = Instance.new("BoolValue")
							m1STUNT.Name = "eStun"
							m1STUNT.Parent = eChar
							game.Debris:AddItem(m1STUNT,1)

							---------------------------------------------------------------------------------
							eHum:TakeDamage(dmg*2)
							if eHum and eHum:FindFirstChild("Animator") then
								local Hit = eHum:LoadAnimation(eAnims[2])
								Hit:Play()
							end

							local HeavyPunch = Sounds[3]:Clone()
							HeavyPunch.Parent = eHum.Parent.HumanoidRootPart
							HeavyPunch:Play()
							game.Debris:AddItem(HeavyPunch,.6)


						end 

					end
				end
			end)
			game.Debris:AddItem(weld,hitboxtime)
			game.Debris:AddItem(hitbox,hitboxtime)
			debounce = false
		elseif count == 5 and State ==  Enum.HumanoidStateType.Freefall then
			hum:LoadAnimation(anims[6]):Play()
			
			wait(WaitTime)			
			local hitbox =  script.HitBox:Clone()
			hitbox.Parent = char

			hitbox.CFrame =  char.HumanoidRootPart.CFrame + char.HumanoidRootPart.CFrame.LookVector*2
			hitbox.Orientation = char.HumanoidRootPart.Orientation + Vector3.new(0,-90,0)
			local weld =  Instance.new("WeldConstraint")
			weld.Part0 = char.HumanoidRootPart
			weld.Part1 =  hitbox
			weld.Parent =  weld.Part0

			local debounce = false 

			hitbox.Touched:Connect(function(hit)
				if hit.Parent ~= char and hit.Parent:IsA("Model")and hit.Parent:FindFirstChild("Humanoid")then
					if debounce == false then
						debounce = true
						hitbox:Destroy()
						local eChar =  hit.Parent
						local eHumrp = eChar:FindFirstChild("HumanoidRootPart")
						local eHum =  eChar:FindFirstChild("Humanoid")
						
						if eChar:FindFirstChild("HitBox")then
							game.Debris:AddItem(eChar:FindFirstChild("HitBox"),0)
						end
						
						if eChar:FindFirstChild("IsBlocking") then
							BlockingEvent:Fire(eChar,count,char)
						else

							for i,v in pairs(Humrp:GetChildren())do
								if v.Name == "MoveMe"then
									v:Destroy()
								end
							end
							for i,v in pairs(eHumrp:GetChildren())do
								if v.Name == "SmallMoveVel"then
									v:Destroy()
								end
							end

						

							if game.Players:GetPlayerFromCharacter(eChar) then
								local eP =  game.Players:GetPlayerFromCharacter(eChar)
								shakeCamera(eP,2,20,1,1,1)

							end



							local HitEffect = Effects[2]:Clone()
							HitEffect.Parent =  eHumrp	
							HitEffect.Color = ColorSequence.new {ColorSequenceKeypoint.new(0,Color3.fromRGB(255, 0, 0)),ColorSequenceKeypoint.new(1, Color3.fromRGB(255, 0, 0))}
							HitEffect:Emit(1)
							game.Debris:AddItem(HitEffect,.5)	


							for i,v in pairs (eHumrp:GetChildren())do
								if v:IsA("BodyVelocity")then 
									v:destroy()
								end	
							end





							local PushVel =  Instance.new("BodyVelocity",eHumrp)
							PushVel.Velocity = Humrp.CFrame.LookVector* 60 + Vector3.new(0,-30,0)
							PushVel.MaxForce =  Vector3.new(10000,10000,10000)


							game.Debris:AddItem(PushVel,.3)




							-----------------------------------------------------------
							local m1STUNT = Instance.new("BoolValue")
							m1STUNT.Name = "eStun"
							m1STUNT.Parent = eChar
							game.Debris:AddItem(m1STUNT,1)

							---------------------------------------------------------------------------------
							eHum:TakeDamage(dmg*2)
							if eHum and eHum:FindFirstChild("Animator") then
								local Hit = eHum:LoadAnimation(eAnims[2])
								Hit:Play()
							end

							local HeavyPunch = Sounds[6]:Clone()
							HeavyPunch.Parent = eHum.Parent.HumanoidRootPart
							HeavyPunch:Play()
							game.Debris:AddItem(HeavyPunch,.862)
						end

					end
				end
			end)
			game.Debris:AddItem(weld,hitboxtime)
			game.Debris:AddItem(hitbox,hitboxtime)
			debounce = false
		end

		--------------------------------------- After Punch

		if count == 5 then 
			CanAttack.Value = false 
			wait(.8)
			CanAttack.Value = true
		end

		Event:FireClient(player)


	else 
		Event:FireClient(player)
	end

end)
