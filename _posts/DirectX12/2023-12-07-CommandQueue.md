---
title: "CommandQueue란?"

categories:
  - DirectX12
tags:
  - DirectX12

author_profile: false

sidebar:
  nav: "docs"

date: 2023-12-07
last_modified_at: 2023-12-07
---


# 🙇‍♀️CommandQueue

DirectX12이전에는 일감을 주면 바로바로 일을 했는데 DirectX12로 넘어오면서 일감 목록을 넘기고 그것으로 일을 수행해야한다.  

<br>

## 🪐CommandQueue.h 분석

**CommandQueue.h 전체코드**
---
```cpp
#pragma once

// 전방선언!
class SwapChain;
class DescriptorHeap;

class CommandQueue
{
public:
	~CommandQueue();

	void Init(ComPtr<ID3D12Device> device, shared_ptr<SwapChain> swapChain, shared_ptr<DescriptorHeap> descHeap);
	void WaitSync();

	void RenderBegin(const D3D12_VIEWPORT* vp, const D3D12_RECT* rect);
	void RenderEnd();

	ComPtr<ID3D12CommandQueue> GetCmdQueue() { return _cmdQueue; }

private:
	// CommandQueue : DX12에 등장
	// 외주를 요청할 때, 하나씩 요청하면 비효율적
	// [외주 목록]에 일감을 차곡차곡 기록했다가 한 방에 요청하는 것
	ComPtr<ID3D12CommandQueue>			_cmdQueue;
	ComPtr<ID3D12CommandAllocator>		_cmdAlloc;
	ComPtr<ID3D12GraphicsCommandList>	_cmdList;

	// Fence : 울타리(?)
	// CPU / GPU 동기화를 위한 간단한 도구
	ComPtr<ID3D12Fence>					_fence;
	uint32								_fenceValue = 0;
	HANDLE								_fenceEvent = INVALID_HANDLE_VALUE;

	shared_ptr<SwapChain>				_swapChain;
	shared_ptr<DescriptorHeap>			_descHeap;

};
```

커맨드 패턴과 비슷하다.  
일감이 생기면 당장 처리하지 않고, 나중에 처리할 수 있게 Queue에 일감을 넣고 나중에 누군가가 일감을 처리하는 개념.  

`ComPtr<ID3D12CommandQueue>` 에 일감을 넣어주고, `ComPtr<ID3D12CommandAllocator>`는 할당자인데 메모리 공간을 관리하는 것이다.  
`ComPtr<ID3D12GraphicsCommandList>`는 일감 목록을 관리한다.  자주 사용될 것 중에 하나이다.  

 Fence는 마이너하다고 한다.  
 CPU와 GPU를 동기화 시켜주는 가장 간단한 도구 중에 하나이다.  
 구현부에서 코드를 보면서 자세히 분석해보자.  

 ## 🪐CommandQueue.cpp 분석

**CommandQueue.cpp 전체코드**
---
 ```cpp
 #include "pch.h"
#include "CommandQueue.h"
#include "SwapChain.h"
#include "DescriptorHeap.h"

CommandQueue::~CommandQueue()
{
	::CloseHandle(_fenceEvent);
}

void CommandQueue::Init(ComPtr<ID3D12Device> device, shared_ptr<SwapChain> swapChain, shared_ptr<DescriptorHeap> descHeap)
{
	_swapChain = swapChain;
	_descHeap = descHeap;

	D3D12_COMMAND_QUEUE_DESC queueDesc = {};
	queueDesc.Type = D3D12_COMMAND_LIST_TYPE_DIRECT;
	queueDesc.Flags = D3D12_COMMAND_QUEUE_FLAG_NONE;

	device->CreateCommandQueue(&queueDesc, IID_PPV_ARGS(&_cmdQueue));

	// - D3D12_COMMAND_LIST_TYPE_DIRECT : GPU가 직접 실행하는 명령 목록
	device->CreateCommandAllocator(D3D12_COMMAND_LIST_TYPE_DIRECT, IID_PPV_ARGS(&_cmdAlloc));

	// GPU가 하나인 시스템에서는 0으로
	// DIRECT or BUNDLE
	// Allocator
	// 초기 상태 (그리고 명령은 nullptr로 지정)
	device->CreateCommandList(0, D3D12_COMMAND_LIST_TYPE_DIRECT, _cmdAlloc.Get(), nullptr, IID_PPV_ARGS(&_cmdList));

	// CommandList는 Close / Open 상태가 있는데
	// Open 상태에서 Command를 넣다가 Close한 다음 제출하는 개념
	_cmdList->Close();

	// CreateFence
	// - CPU와 GPU의 동기화 수단으로 쓰인다
	device->CreateFence(0, D3D12_FENCE_FLAG_NONE, IID_PPV_ARGS(&_fence));
	_fenceEvent = ::CreateEvent(nullptr, FALSE, FALSE, nullptr);
}

void CommandQueue::WaitSync()
{
	// Advance the fence value to mark commands up to this fence point.
	_fenceValue++;

	// Add an instruction to the command queue to set a new fence point.
	// Because we are on the GPU timeline, the new fence point won't be set until the GPU finishes
	// processing all the commands prior to this Signal().
	_cmdQueue->Signal(_fence.Get(), _fenceValue);

	// Wait until the GPU has completed commands up to this fence point.
	if (_fence->GetCompletedValue() < _fenceValue)
	{
		// Fire event when GPU hits current fence.
		_fence->SetEventOnCompletion(_fenceValue, _fenceEvent);

		// Wait until the GPU hits current fence event is fired.
		::WaitForSingleObject(_fenceEvent, INFINITE);
	}

	// 효율적인 방식은 아님
	// CPU가 GPU의 일이 끝날 때까지 기다리고 있음
}

void CommandQueue::RenderBegin(const D3D12_VIEWPORT* vp, const D3D12_RECT* rect)
{
	// vector.clear()처럼 capacity는 남아있는 개념. 메모리가 줄어들진 않는다.
	_cmdAlloc->Reset();
	_cmdList->Reset(_cmdAlloc.Get(), nullptr);

	D3D12_RESOURCE_BARRIER barrier = CD3DX12_RESOURCE_BARRIER::Transition(
		_swapChain->GetCurrentBackBufferResource().Get(),
		D3D12_RESOURCE_STATE_PRESENT, // 화면 출력
		D3D12_RESOURCE_STATE_RENDER_TARGET); // 외주 결과물
	
	_cmdList->ResourceBarrier(1, &barrier);

	// Set the viewport and scissor rect, This needs to be reset whenever the command list is reset.
	_cmdList->RSSetViewports(1, vp);
	_cmdList->RSSetScissorRects(1, rect);

	// Specify the buffers we are going to render to.
	D3D12_CPU_DESCRIPTOR_HANDLE backBufferView = _descHeap->GetBackBufferView();
	_cmdList->ClearRenderTargetView(backBufferView, Colors::LightSteelBlue, 0, nullptr);
	_cmdList->OMSetRenderTargets(1, &backBufferView, FALSE, nullptr);
}

void CommandQueue::RenderEnd()
{
	D3D12_RESOURCE_BARRIER barrier = CD3DX12_RESOURCE_BARRIER::Transition(
		_swapChain->GetCurrentBackBufferResource().Get(),
		D3D12_RESOURCE_STATE_RENDER_TARGET, // 외주 결과물
		D3D12_RESOURCE_STATE_PRESENT); // 화면 출력

	_cmdList->ResourceBarrier(1, &barrier);
	_cmdList->Close();

	// 커맨드 리스트 수행
	ID3D12CommandList* cmdListArr[] = { _cmdList.Get() };
	_cmdQueue->ExecuteCommandLists(_countof(cmdListArr), cmdListArr);

	_swapChain->Present();

	// Wait until frame commands are complete. This waiting is inefficient and is done for simplicity.
	// Later we will show how to organize our rendering code so we do not have to wait per frame.
	WaitSync();

	_swapChain->SwapIndex();
}
```

함수별로 구분해서 분석해보자.  

**Init**
---
```cpp
void CommandQueue::Init(ComPtr<ID3D12Device> device, shared_ptr<SwapChain> swapChain, shared_ptr<DescriptorHeap> descHeap)
{
	_swapChain = swapChain;
	_descHeap = descHeap;

	D3D12_COMMAND_QUEUE_DESC queueDesc = {};
	queueDesc.Type = D3D12_COMMAND_LIST_TYPE_DIRECT;
	queueDesc.Flags = D3D12_COMMAND_QUEUE_FLAG_NONE;

	device->CreateCommandQueue(&queueDesc, IID_PPV_ARGS(&_cmdQueue));

	// - D3D12_COMMAND_LIST_TYPE_DIRECT : GPU가 직접 실행하는 명령 목록
	device->CreateCommandAllocator(D3D12_COMMAND_LIST_TYPE_DIRECT, IID_PPV_ARGS(&_cmdAlloc));

	// GPU가 하나인 시스템에서는 0으로
	// DIRECT or BUNDLE
	// Allocator
	// 초기 상태 (그리고 명령은 nullptr로 지정)
	device->CreateCommandList(0, D3D12_COMMAND_LIST_TYPE_DIRECT, _cmdAlloc.Get(), nullptr, IID_PPV_ARGS(&_cmdList));

	// CommandList는 Close / Open 상태가 있는데
	// Open 상태에서 Command를 넣다가 Close한 다음 제출하는 개념
	_cmdList->Close();

	// CreateFence
	// - CPU와 GPU의 동기화 수단으로 쓰인다
	device->CreateFence(0, D3D12_FENCE_FLAG_NONE, IID_PPV_ARGS(&_fence));
	_fenceEvent = ::CreateEvent(nullptr, FALSE, FALSE, nullptr);
}
```

device가 engine에서의 핵심이라고 했는데 CommandQueue의 부품들 역시 device를 통해서 만들어주고 있다.  
`D3D12_COMMAND_QUEUE_DESC`라는 CommandQueue의 설명서를 작성하고  
`device->CreateCommandQueue(&queueDesc, IID_PPV_ARGS(&_cmdQueue));`를 통해서 실제로 CommandQueue를 초기화한다.  
초기화는 Device때와 마찬가지로 `IID_PPV_ARGS`를 사용하고 있다.  

세부적으로 하는 기능들은 굳이 알아보지 않아도 된다고 한다.  

CommandList 생성하는 곳을 보면,  
`device->CreateCommandList(0, D3D12_COMMAND_LIST_TYPE_DIRECT, _cmdAlloc.Get(), nullptr, IID_PPV_ARGS(&_cmdList));`인데 위에서 생성한 `_cmdAlloc`을 사용하여 메모리 공간을 확보하고 있다.  
stl::vector와 비슷한 개념이라고 한다.  
비유를 하자면 vector.size가 _cmdList와 비슷하고 vector.capacity가 _cmdAlloc과 비슷한 개념이다.

그리고 일감 목록에서는 Close를 해서 일감 목록을 제출할 준비를 완료한다.  

device를 통해 Fence역시 생성하는데 생성만 하고 있다.  
`::CreateEvent`는 DirectX에서만 국한된게 아니라 멀티쓰레드 등 다양하게 사용한다.  
이벤트는 신호등으로 비유를 하는데 빨간불일 때는 아무것도 안하다가 파란불일 때는 이동을한다.  
이것과 마찬가지로 특정 조건이 올 때까지 기다렸다가 조건을 만족하면 일을 수행한다.  

그리고 소멸자에서 이벤트를 꺼주는 일도 하는 것을 볼 수 있다.  

**WaitSync**
---
```cpp
void CommandQueue::WaitSync()
{
	// Advance the fence value to mark commands up to this fence point.
	_fenceValue++;

	// Add an instruction to the command queue to set a new fence point.
	// Because we are on the GPU timeline, the new fence point won't be set until the GPU finishes
	// processing all the commands prior to this Signal().
	_cmdQueue->Signal(_fence.Get(), _fenceValue);

	// Wait until the GPU has completed commands up to this fence point.
	if (_fence->GetCompletedValue() < _fenceValue)
	{
		// Fire event when GPU hits current fence.
		_fence->SetEventOnCompletion(_fenceValue, _fenceEvent);

		// Wait until the GPU hits current fence event is fired.
		::WaitForSingleObject(_fenceEvent, INFINITE);
	}

	// 효율적인 방식은 아님
	// CPU가 GPU의 일이 끝날 때까지 기다리고 있음
}
```

어떤 값을 주고 그 일이 끝날 때까지 기다리는 방식이다.  
효율적인 방식이 아니다!  
CPU가 GPU의 일이 끝날때까지 기다리는 방식이기 때문이다.  
게임은 실시간으로 계속 연산을 해야하는데 지금은 간단하게 공부하기 위해서 이렇게 짰다.  

**RenderBegin**
---
```cpp
void CommandQueue::RenderBegin(const D3D12_VIEWPORT* vp, const D3D12_RECT* rect)
{
	// vector.clear()처럼 capacity는 남아있는 개념. 메모리가 줄어들진 않는다.
	_cmdAlloc->Reset();
	_cmdList->Reset(_cmdAlloc.Get(), nullptr);

	D3D12_RESOURCE_BARRIER barrier = CD3DX12_RESOURCE_BARRIER::Transition(
		_swapChain->GetCurrentBackBufferResource().Get(),
		D3D12_RESOURCE_STATE_PRESENT, // 화면 출력
		D3D12_RESOURCE_STATE_RENDER_TARGET); // 외주 결과물
	
	_cmdList->ResourceBarrier(1, &barrier);

	// Set the viewport and scissor rect, This needs to be reset whenever the command list is reset.
	_cmdList->RSSetViewports(1, vp);
	_cmdList->RSSetScissorRects(1, rect);

	// Specify the buffers we are going to render to.
	D3D12_CPU_DESCRIPTOR_HANDLE backBufferView = _descHeap->GetBackBufferView();
	_cmdList->ClearRenderTargetView(backBufferView, Colors::LightSteelBlue, 0, nullptr);
	_cmdList->OMSetRenderTargets(1, &backBufferView, FALSE, nullptr);
}
```




**RenderEnd**
---
```cpp
void CommandQueue::RenderEnd()
{
	D3D12_RESOURCE_BARRIER barrier = CD3DX12_RESOURCE_BARRIER::Transition(
		_swapChain->GetCurrentBackBufferResource().Get(),
		D3D12_RESOURCE_STATE_RENDER_TARGET, // 외주 결과물
		D3D12_RESOURCE_STATE_PRESENT); // 화면 출력

	_cmdList->ResourceBarrier(1, &barrier);
	_cmdList->Close();

	// 커맨드 리스트 수행
	ID3D12CommandList* cmdListArr[] = { _cmdList.Get() };
	_cmdQueue->ExecuteCommandLists(_countof(cmdListArr), cmdListArr);

	_swapChain->Present();

	// Wait until frame commands are complete. This waiting is inefficient and is done for simplicity.
	// Later we will show how to organize our rendering code so we do not have to wait per frame.
	WaitSync();

	_swapChain->SwapIndex();
}
```